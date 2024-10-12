#Google Cloud Storage Hierarchy in .NET

<img src="https://vienna-wv.com/images/tree.jpg" class="main-blog-image" style="width: 250px" />

Google's [Cloud Storage Browser](https://console.cloud.google.com/storage/browser) perpetrates a fiction of files and folders that doesn't exist. The [Google Cloud Storage (GCS) API](https://cloud.google.com/storage/docs/json_api/) only has two concepts: buckets and objects.

1. A bucket is a container of objects and needs a globally unique name.

2. An object has a name, a content type and content. The name can be simple, e.g. "foo.txt" or it can have slashes in it, e.g. "foo/bar.txt" or even just "quux/". However, from the GCS API point of view, there's no difference -- the only container is a bucket.

For example, I can write a program using [the .NET GCS client lib](https://github.com/GoogleCloudPlatform/google-cloud-dotnet#google-cloud-storage) from [NuGet](https://www.nuget.org/packages/Google.Storage.V1/) that looks like this:

```c#
void ListBucketsAndObjects(string projectId) {
  var client = StorageClient.Create();

  foreach (var bucket in client.ListBuckets(projectId)) {
    Console.WriteLine($"{bucket.Name}/");
    foreach (var obj in client.ListObjects(bucket.Name, null)) {
      Console.WriteLine($"  {obj.Name}");
    }
  }
}
```

Given objects with names as described above, this would be the output:

```
csells-bucket-1/
  foo.txt
  foo/bar.txt
  quux/
```

However, if you surf to the [Storage Browser](https://console.cloud.google.com/storage/browser) to see the project with these three objects, you'll see something that looks like a normal file/folder browser:

<img src="http://sellsbrothers.com/public/post-images/gcs-bhelper-1.png" />

## Implicit and Explicit Folders
The Storage Browser has interpreted one of the objects as a file and two of them as folders, one implicit and one explicit. The implicit object folder comes from the slash in "foo/bar.txt"; the slash is used as a delimiter that means "folder" as far as the Storage Browser is concerned.

The explicit folder comes from an object with a name that ends in a slash. You can create one by pressing the Create Folder button in the Storage Explorer or with the following lines of code:

```c#
var client = StorageClient.Create();
client.UploadObject(bucketName, "quux/", "", Stream.Null);

```

## Working with Folders
When you're working with buckets and objects, the ListBuckets and ListObjects methods work just fine. However, if you'd like to navigate the fictional hierarchy of files and folders the way that the Storage Browser does, you can use the [BrowserHelper](https://github.com/csells/BucketHelperSample/) (a piece of .NET helper code I put together for just this purpose):

```c#
void ListBucketsFilesAndFolders(string projectId) {
  var client = StorageClient.Create();

  foreach (var bucket in client.ListBuckets(projectId)) {
    ListFilesAndFolders(client, bucket.Name);
  }
}

void ListFilesAndFolders(StorageClient client, string bucket, string parentFolder = "", string indent = "") {
  string shortName = parentFolder == "" ? bucket : BucketHelper.ShortName(parentFolder);
  Console.WriteLine($"{indent}{shortName}/");
  indent += "  ";

  foreach (var file in client.ListFiles(bucket, parentFolder)) {
    Console.WriteLine($"{indent}{file.ShortName()}");
  }

  foreach (var folder in client.ListFolders(bucket, parentFolder)) {
    ListFilesAndFolders(client, bucket, folder, indent);
  }
}
```

The BucketHelper extension class provides the ShortName, ListFiles and ListFolders functions in the sample above. ListFiles and ListFolders are provided on the existing .NET client library types instead of providing a whole new set of wrapped types, which largely just get in the way.

The output for the same list of objects looks like this:

```
csells-bucket-1/
  foo.txt
  foo/
    bar.txt
  quux/
```

The implicit and explicit folders are folded together into a list of strings at each level, so your code doesn't have to care which is which. However, if you do care, a call to StorageClient.GetObject returns an object or throws an exception depending on whether it's explicit or implicit.

When creating your objects, your code doesn't have to explicitly create folders, since implicit folders are first class citizens as far as the BrowserHelper and the Storage Browser are concerned. However, if you'd like to create a folder explicitly, BrowserHelper provides a helper for that, too:

```c#
var client = StorageClient.Create();
var folderObj = client.CreateFolder(bucketName, "baaz/");

```

##Where Are We?
The BucketHelper is an extension to [the hand-crafted Google Cloud Storage client library for .NET](https://github.com/GoogleCloudPlatform/google-cloud-dotnet#google-cloud-storage). It helps to provide access to the same fictional hierarchy that the Cloud Storage Browser provides over your buckets and objects. Enjoy.