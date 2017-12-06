---
title: FileSystem
---

Provides access to a file system stored locally on the device. Each Expo app has a separate file systems and has no access to the file system of other Expo apps. The API takes `file://` URIs pointing to local files on the device to identify files. Each app only has read and write access to locations under the following directories:

-   **`Expo.FileSystem.documentDirectory`**

  `file://` URI pointing to the directory where user documents for this app will be stored. Files stored here will remain until explicitly deleted by the app. Ends with a trailing `/`. Example uses are for files the user saves that they expect to see again.

-   **`Expo.FileSystem.cacheDirectory`**

  `file://` URI pointing to the directory where temporary files used by this app will be stored. Files stored here may be automatically deleted by the system when low on storage. Example uses are for downloaded or generated files that the app just needs for one-time usage.

So, for example, the URI to a file named `'myFile'` under `'myDirectory'` in the app's user documents directory would be `Expo.FileSystem.documentDirectory + 'myDirectory/myFile'`.


### `Expo.FileSystem.getInfoAsync(fileUri, options)`

Get metadata information about a file or directory.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the file or directory.

-   **options (_object_)** -- A map of options:

    -   **md5 (_boolean_)** -- Whether to return the MD5 hash of the file. `false` by default.

#### Returns

If no item exists at this URI, returns `{ exists: false, isDirectory: false }`. Else returns an object with the following fields:

-   **exists (_boolean_)** -- `true`.

-   **isDirectory (_boolean_)** -- `true` if this is a directory, `false` if it is a file

-   **modificationTime (_number_)** -- The last modification time of the file expressed in seconds since epoch.

-   **size (_number_)** -- The size of the file in bytes.

-   **uri (_string_)** -- A `file://` URI pointing to the file. This is the same as the `fileUri` input parameter.

-   **md5 (_string_)** -- Present if the `md5` option was truthy. Contains the MD5 hash of the file.


### `Expo.FileSystem.readAsStringAsync(fileUri)`

Read the entire contents of a file as a string.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the file or directory.

#### Returns

A string containing the entire contents of the file.

### `Expo.FileSystem.writeAsStringAsync(fileUri, contents)`

Write the entire contents of a file as a string.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the file or directory.

-   **contents (_string_)** -- The string to replace the contents of the file with.

### `Expo.FileSystem.deleteAsync(fileUri, options)`

Delete a file or directory. If the URI points to a directory, the directory and all its contents are recursively deleted.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the file or directory.

-   **options (_object_)** -- A map of options:

  -   **idempotent (_boolean_)** -- If `true`, don't throw an error if there is no file or directory at this URI. `false` by default.

### `Expo.FileSystem.moveAsync(options)`

Move a file or directory to a new location.

#### Arguments

-   **options (_object_)** -- A map of options:

    -   **from (_string_)** -- `file://` URI to the file or directory at its original location.

    -   **to (_string_)** -- `file://` URI to the file or directory at what should be its new location.

### `Expo.FileSystem.copyAsync(options)`

Create a copy of a file or directory. Directories are recursively copied with all of their contents.

#### Arguments

-   **options (_object_)** -- A map of options:

    -   **from (_string_)** -- `file://` URI to the file or directory to copy.

    -   **to (_string_)** -- The `file://` URI to the new copy to create.

### `Expo.FileSystem.makeDirectoryAsync(fileUri, options)`

Create a new empty directory.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the new directory to create.

-   **options (_object_)** -- A map of options:

    -   **intermediates (_boolean_)** -- If `true`, create any non-existent parent directories when creating the directory at `fileUri`. If `false`, raises an error if any of the intermediate parent directories does not exist. `false` by default.

### `Expo.FileSystem.readDirectoryAsync(fileUri)`

Enumerate the contents of a directory.

#### Arguments

-   **fileUri (_string_)** -- `file://` URI to the directory.

#### Returns

An array of strings, each containing the name of a file or directory contained in the directory at `fileUri`.

### `Expo.FileSystem.downloadAsync(uri, fileUri, options)`

Download the contents at a remote URI to a file in the app's file system.

#### Example

```javascript
FileSystem.downloadAsync(
  'http://techslides.com/demos/sample-videos/small.mp4',
  FileSystem.documentDirectory + 'small.mp4'
)
  .then(({ uri }) => {
    console.log('Finished downloading to ', uri);
  })
  .catch(error => {
    console.error(error);
  });
```

#### Arguments

-   **url (_string_)** -- The remote URI to download from.

-   **fileUri (_string_)** -- The local URI of the file to download to. If there is no file at this URI, a new one is created. If there is a file at this URI, its contents are replaced.

-   **options (_object_)** -- A map of options:

    -   **md5 (_boolean_)** -- If `true`, include the MD5 hash of the file in the returned object. `false` by default. Provided for convenience since it is common to check the integrity of a file immediately after downloading.

#### Returns

Returns an object with the following fields:

-   **uri (_string_)** -- A `file://` URI pointing to the file. This is the same as the `fileUri` input parameter.

-   **status (_number_)** -- The HTTP status code for the download network request.

-   **headers (_object_)** -- An object containing all the HTTP header fields and their values for the download network request. The keys and values of the object are the header names and values respectively.

-   **md5 (_string_)** -- Present if the `md5` option was truthy. Contains the MD5 hash of the file.

### `Expo.FileSystem.createDownloadResumable(uri, fileUri, options, callback, resumeData)`

Create a `DownloadResumable` object which can start, pause, and resume a download of contents at a remote URI to a file in the app's file system.  Please note:  You need to call `downloadAsync()`, on a `DownloadResumable` instance to initiate the download.  The `DownloadResumable` object has a callback that provides download progress updates.  Downloads can be resumed across app restarts by using `AsyncStorage` to store the `DownloadResumable.savable()` object for later retrieval.  The `savable` object contains the arguments required to initialize a new `DownloadResumable` object to resume the download after an app restart.

#### Arguments

-   **url (_string_)** -- The remote URI to download from.

-   **fileUri (_string_)** -- The local URI of the file to download to. If there is no file at this URI, a new one is created. If there is a file at this URI, its contents are replaced.

-   **options (_object_)** -- A map of options:

    -   **md5 (_boolean_)** -- If `true`, include the MD5 hash of the file in the returned object. `false` by default. Provided for convenience since it is common to check the integrity of a file immediately after downloading.

    -   **headers (_object_)** -- An object containing any additional HTTP header fields required for the request.  The keys and values of the object are the header names and values respectively.

-   **callback (_function_)** --
    This function is called on each data write to update the download progress.  An object with the following fields are passed:
    - **totalBytesWritten (_number_)** -- The total bytes written by the download operation.
    - **totalBytesExpectedToWrite (_number_)** -- The total bytes expected to be written by the download operation.

-   **resumeData (_string_)** -- The string which allows the api to resume a paused download.  This is set on the `DownloadResumable` object automatically when a download is paused.  When initializing a new `DownloadResumable` this should be `null`.

### `Expo.FileSystem.DownloadResumable.downloadAsync()`

Download the contents at a remote URI to a file in the app's file system.

#### Returns

Returns an object with the following fields:

-   **uri (_string_)** -- A `file://` URI pointing to the file. This is the same as the `fileUri` input parameter.

-   **status (_number_)** -- The HTTP status code for the download network request.

-   **headers (_object_)** -- An object containing all the HTTP header fields and their values for the download network request. The keys and values of the object are the header names and values respectively.

-   **md5 (_string_)** -- Present if the `md5` option was truthy. Contains the MD5 hash of the file.

### `Expo.FileSystem.DownloadResumable.pauseAsync()`

Pause the current download operation.  `resumeData` is added to the `DownloadResumable` object after a successful pause operation.  Returns an object that can be saved with `AsyncStorage` for future retrieval (the same object that is returned from calling `Expo.FileSystem.DownloadResumable.savable()`.  Please see the example below.

#### Returns 

Returns an object with the following fields:

-   **url (_string_)** -- The remote URI to download from.

-   **fileUri (_string_)** -- The local URI of the file to download to. If there is no file at this URI, a new one is created. If there is a file at this URI, its contents are replaced.

-   **options (_object_)** -- A map of options:

    -   **md5 (_boolean_)** -- If `true`, include the MD5 hash of the file in the returned object. `false` by default. Provided for convenience since it is common to check the integrity of a file immediately after downloading.

-   **resumeData (_string_)** -- The string which allows the API to resume a paused download.

### `Expo.FileSystem.DownloadResumable.resumeAsync()`

Resume a paused download operation.

#### Returns

Returns an object with the following fields:

-   **uri (_string_)** -- A `file://` URI pointing to the file. This is the same as the `fileUri` input parameter.

-   **status (_number_)** -- The HTTP status code for the download network request.

-   **headers (_object_)** -- An object containing all the HTTP header fields and their values for the download network request. The keys and values of the object are the header names and values respectively.

-   **md5 (_string_)** -- Present if the `md5` option was truthy. Contains the MD5 hash of the file.

### `Expo.FileSystem.DownloadResumable.savable()`

Returns an object which can be saved with `AsyncStorage` for future retrieval.

#### Returns

Returns an object with the following fields:

-   **url (_string_)** -- The remote URI to download from.

-   **fileUri (_string_)** -- The local URI of the file to download to. If there is no file at this URI, a new one is created. If there is a file at this URI, its contents are replaced.

-   **options (_object_)** -- A map of options:

    -   **md5 (_boolean_)** -- If `true`, include the MD5 hash of the file in the returned object. `false` by default. Provided for convenience since it is common to check the integrity of a file immediately after downloading.

-   **resumeData (_string_)** -- The string which allows the api to resume a paused download.

#### Example

```javascript
const callback = downloadProgress => {
  const progress =
    downloadProgress.totalBytesWritten /
    downloadProgress.totalBytesExpectedToWrite;
  this.setState({
    downloadProgress: progress,
  });
};

const downloadResumable = FileSystem.createDownloadResumable(
  'http://techslides.com/demos/sample-videos/small.mp4',
  FileSystem.documentDirectory + 'small.mp4',
  {},
  callback
);

try {
  const { uri } = await downloadResumable.downloadAsync();
  console.log('Finished downloading to ', uri);
} catch (e) {
  console.error(e);
}

try {
  await downloadResumable.pauseAsync();
  console.log('Paused download operation, saving for future retrieval');
  AsyncStorage.setItem(
    'pausedDownload',
    JSON.stringify(downloadResumable.savable())
  );
} catch (e) {
  console.error(e);
}

try {
  const { uri } = await downloadResumable.resumeAsync();
  console.log('Finished downloading to ', uri);
} catch (e) {
  console.error(e);
}

//To resume a download across app restarts, assuming the the DownloadResumable.savable() object was stored:
const downloadSnapshotJson = await AsyncStorage.getItem('pausedDownload');
const downloadSnapshot = JSON.parse(downloadJson);
const downloadResumable = new FileSystem.DownloadResumable(
  downloadSnapshot.url,
  downloadSnapshot.fileUri,
  downloadSnapshot.options,
  callback,
  downloadSnapshot.resumeData
);

try {
  const { uri } = await downloadResumable.resumeAsync();
  console.log('Finished downloading to ', uri);
} catch (e) {
  console.error(e);
}
```
