# B站缓存视频翻译器
这是一个用 Python 写成的简单 GUI 程序, 受到视频 https://www.bilibili.com/video/BV1nx4y16737 的启发。
在之前版本的哔哩哔哩客户端缓存视频, 下载的 M4S 文件是可以直接合成成 MP4 的。但是新版的客户端缓存的 M4S 文件似乎被加了密, 也许是为了保护创作者的版权。
而我们只是想下载视频拷贝到其它设备观看之类的。
因此, 我制作了B站缓存视频翻译器。
## 实现与原理
新版客户端缓存的 M4S 视频文件，如果用 Notepad++ 之类的程序打开，可以发现其二进制开头有 9 个 0，还有其他的一些符号。我们需要删除这些符号。函数 mixM4S 的实现:
```py
def fixM4S(targetPath:str, bufSize:int = 256 * 1024 * 1024) -> str:
	assert bufSize > 0
	tempOutputPath = f"{os.path.dirname(targetPath)}/temp_{os.path.basename(targetPath)}"
	with open(targetPath, 'rb') as targetFile:
		header = targetFile.read(32)
		newHeader = header.replace(b'000000000', b'')
		newHeader = newHeader.replace(b'$', b' ')
		newHeader = newHeader.replace(b'avc1', b'')
		with open(tempOutputPath, 'wb') as outputFile:
			outputFile.write(newHeader)
			i = targetFile.read(bufSize)
			while i:
				outputFile.write(i)
				i = targetFile.read(bufSize)
	return tempOutputPath
```
这部分函数引用了作者 @无知以言 (https://space.bilibili.com/30739293) 并修改了部分。
## 使用
运行 bilicvtran.exe, 选择 "选择 M4S 文件", 会弹出两次对话框, 第一次请打开缓存视频位置的 M4S 视频文件, 第二次打开缓存视频位置的 M4S 音频文件。普遍来说, 音频文件比视频文件小得多, 所以可以通过大小判断。然后, 选择 "开始转换", 选择保存位置。日志将在 CMD 窗口输出。
