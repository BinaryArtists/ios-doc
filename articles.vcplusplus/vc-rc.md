### VS中的资源


## 怎么把rc文件打包进exe？

	1. [用vs 怎么把一个资源文件打包进exe](http://bbs.csdn.net/topics/390787154)

	> 编辑.rc文件，但资源中的内容只能被当作应用程序附带的数据来读取，不能执行。

	2. [VC从EXE中提取资源文件](http://blog.csdn.net/itjobtxq/article/details/8465278)

	```
	bool ExtractFileWithRes(unsigned int nResID, LPCTSTR pszResType, LPCTSTR pszSavePath)	{
	 	if (NULL == pszResType || NULL == pszSavePath) {
			return false;
		}

	 	HRSRC hResource = FindResource(GetModuleHandle(NULL), MAKEINTRESOURCE(nResID), pszResType);  // 查找目标资源
	 	if (hResource != NULL) {
	  	// 加载资源
	  	HGLOBAL hg = LoadResource(GetModuleHandle(NULL), hResource);
	  	if (hg != NULL) {
	   		// 锁定资源
	   		LPVOID pData = LockResource(hg);
	   		if (pData != NULL) {
	    		// 获取资源大小
	    		DWORD dwSize = SizeofResource(GetModuleHandle(NULL), hResource);
	    		FILE *fp = fopen((const char *)(_bstr_t)pszSavePath, "wb");
	    		if (fp != NULL) {
	     			DWORD dwCount = 0, dwWrite = 0;
	     			while (dwCount < dwSize) {
	      			dwWrite = dwSize - dwCount > 2048 ? 2048 : dwSize - dwCount;
	      			fwrite((char *)pData + dwCount, 1, dwWrite, fp);
	      			dwCount += dwWrite;
	     			}
	     			fclose(fp);
	     			return true;
	    		}
	   		}
	  	}
	 	}
 		return false;
	}
	```
