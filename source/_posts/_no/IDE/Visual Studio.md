

# Visual Studio202217.10.4

[�ٷ��ĵ�](https://learn.microsoft.com/zh-cn/visualstudio/ide/synchronized-settings-in-visual-studio?view=vs-2022)



## ����

��Ŀ  ��������(���Ŀ¼ Debug/Release ƽ̨)
	��������-���� : Windows SDK�汾��ƽ̨���߼������Ա�׼
	��������-C/C++-�Ż� = ����(ReleaseĬ��) / ����(DebugĬ��)
	��������-C/C++-��������-���п�-���̵߳��� DLL =  MD(Ĭ�� ����С ����ϵͳMS����ʱDLL��) / **MT**(������� ���ǲ�����ϵͳDLL)
	��������-C/C++-����-���Ӱ���Ŀ¼������**ͷ�ļ����·��**
	��������-C/C++-Ԥ����ͷ��ʹ��(����ѡ��/Yu)(����Ŀ) / **��ʹ��Ԥ����ͷ**(Precompiled Header .pch)(С��Ŀ����������Ŀ)(�Ƽ�)
	��������-C/C++-������-����-���ӿ�Ŀ¼��${SolutionDir}/x64/Release **lib������path**
	��������-C/C++-������-����-���������xx.lib **lib����**
	��������-���ԣ�����Ŀ¼������

����  ���ù�����
����
����
����
����  ��������
	������-������������ʾ



### ��ݼ�

| ��ݼ�            | ����                                                         |      |
| ----------------- | ------------------------------------------------------------ | ---- |
| ctrl+l+d          | ��ʽ������                                                   |      |
| Ctrl+Shift+�ո�� | �鿴������ʾ��Ϣ<br />����������м䣬�����¼��鿴���ɡ����ߣ�д�����صĺ�������ֱ�Ӵ���shift+(�����¼��鿴���ɡ� |      |
| Shift+F12         | �鿴������������                                             |      |
| ///+enter         | ����ע��                                                     |      |



## ��Ŀ

�Ƽ���sln����Ŀ����

�������SolutionDir
	������Ŀ������Ϊ������Ŀ
		���ã��Ҽ�������� = ����������-��Ŀ������
		�ⲿ������
		ͷ�ļ�
		Դ�ļ�
		��Դ�ļ�
	��̬����Ŀ
	��̬���ӿ���Ŀ(ɾ��framework.h pch.h dllmain.cpp pch.cpp�Ա�д����Linux��)
		dumpbin EXPORTS xxx.dll �鿴DLL�е����ĺ����б�



��֯��Ŀ��
������+��������-C/C++-����-���Ӱ���Ŀ¼
lib��̬��
dll��̬�⾲̬����
	��������-C/C++-������-����-���������xx.lib
dll��̬�⶯̬����



����dll��

```c++
# nn_api.h
#ifdef __cplusplus
extern "C" {
#endif
    
_declspec(dllexport) int __stdcall fun1(const chat* param);
_declspec(dllexport) int __stdcall fun2(const chat* param);
    
#ifdef __cplusplus
}
#endif
```

\__declspec ����dll
__stddcall �����������

������ xx.dll xx.lib x_api.h



��̬���룺

```c++
# nn_api.h
#ifdef __cplusplus
extern "C" {
#endif
    
_declspec(dllimport) int __stdcall fun1(const chat* param);
_declspec(dllimport) int __stdcall fun2(const chat* param);
    
#ifdef __cplusplus
}
#endif
```



��̬���أ�

```c++
int __stdcall fun1(const chat* param);
typedef int(__stdcall* Proc_fun1)(const char* param);  # �����ṩ��GetProcAddress������ǿת
# using Proc_fun1 = int(_stdcall*)(const char* param)  # C++11
# _stdcall*���͵ĺ���ָ�� ����const char* ����int
# ����ָ�붨�壺����ֵ(����ָ����)(��������) 

int __stdcall fun2(const chat* param);
```



1. ��ļ��أ�LoadLibraray��dlopen
2. ��ȡ������ַ GetProcAddress��dlsym
3. ����ĳ��������ɹ���
4. ж��dll FreeLibrary��dlclose

```c++
#include "nn/nn_api"

#include "iostream"
#include <windows.h>

static const char* filename = "libnn.dll";

int main()
{
    /* 1. ����DLL����ǰ��ַ�ռ� */
    HMODELE handle = LoadLibrarayA(filename);
    if (!handle)
    {
        std::cout << "failed to load libraray:" << filename << std::endl;
        return -1;
    }
    
    /* ��ȡdll�����ĺ�����ַ */
    auto func_fun1 = (Proc_fun1)GetProcAddress(handle, "fun1");
    
    /* 3. ����dll*/
    func_fun1();
    
    /* 4. �ͷ�dll */
    FreeLibrary(handle)
    
}

```



### ���lib

1��ͨ��vs��������

��������-C/C++-������-����-���ӿ�Ŀ¼��${SolutionDir}/x64/Release **lib������path**
��������-C/C++-������-����-���������xx.lib **lib����**



2��ͨ����

```c++
#ifdef _DEBUG
#pragma comment(lib,"..\\debug\\LedCtrlBoard.lib")
#else
#pragma comment(lib,"..\\release\\LedCtrlBoard.lib")
#endif
```



3��ֱ����������ѡ��lib�ļ�(�Ƽ�)





## �ļ�

X86  # ���ɵĽ������(��ɾ����������)  # \$(SolutionDir)$(Platform)\$(Configuration)\
	Debug
		projectname.exe
		projectname.pdb
src
	projectame.cpp
bin
dll��̬��
lib��̬��
resprojectname.vcxproj
projectname.vsxproj.filters
projectname.vsxproj.user	
