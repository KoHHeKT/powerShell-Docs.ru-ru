# <a name="new-temporaryfile"></a>New-TemporaryFile
Иногда в сценариях необходимо создать временный файл. Для этого удобно использовать командлет **New-TemporaryFile**:

PS C:\\&gt; $tempFile = New-TemporaryFile

PS C:\\&gt; $tempFile.FullName

C:\\Users\\slee\\AppData\\Local\\Temp\\tmp375.tmp
