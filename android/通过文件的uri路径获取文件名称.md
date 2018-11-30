# android 通过文件的uri路径获取文件名称

``` java

private String getFilepathFromUri(String uriString) {
    Uri uri = Uri.parse(uriString);
    String[] proj = {MediaStore.Images.Media.DATA};
    Cursor cursor = this.getContentResolver().query(uri, proj, null, null, null);
    if (cursor == null)
        return null;
    int columnIndex = cursor.getColumnIndexOrThrow(MediaStore.Images.Media.DATA);
    cursor.moveToFirst();
    String filePath = cursor.getString(columnIndex);
    cursor.close();

    return filePath;
}

```
