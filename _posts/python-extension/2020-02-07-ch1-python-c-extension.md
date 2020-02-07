---
layout: single
title: "Python C/C++ API 바인딩"
tags: ['python', 'C/C++','dev']
date: 2020-02-07 08:00:00
toc: true
toc_label: 목차
toc_icon: "cog"
---

[파이썬 공식 C/C++ 바인딩 튜토리얼(Extending Python Interpreter)](https://docs.python.org/3/extending/index.html)을 보면서 새로 알게 된 것들을 정리해 보았습니다. 지극히 개인적인 기준으로 적은 것이니 내용이 두서없을 수 있습니다.

## Extending Python with C/C++

* C extension은 `CPython` 구현에서만 가능함
  * C 라이브러리 함수나 시스템 콜을 호출해야 하는 경우 `ctypes`나 `cffi`를 이용할 수 있다.

### Simple Example

다음과 같이 작동하는 파이썬 모듈을 만들어본다.

```python
import spam
status = spam.system("ls -l")
```

[파이썬 개발 패키지](https://stackoverflow.com/questions/21530577/fatal-error-python-h-no-such-file-or-directory)를 설치해야 한다. C 모듈 이름을 `spammodule.c`로 만들어 `#include <Python.h>`를 입력한다.

```c
static PyObject *
spam_system(PyObject *self, PyObject *args)
{
    const char *command;
    int sts;

    if (!PyArg_ParseTuple(args, "s", &command))
        return NULL;
    sts = system(command);
    return PyLong_FromLong(sts);
}
```

* 모든 함수는 항상 2개의 인수(`self`, `args`)를 가진다. `self`는 모듈 오브젝트에 대한 포인터를 나타낸다.

* `PyArg_ParseTuple()`을 통해 argument parsing이 가능하다. 제대로 파싱이 되지 않으면 non-zero 값을 리턴한다.

* 만약 void리턴인 경우 다음과 같이 해야 한다.

  ```c
  // Py_RETURN_NONE 매크로 사용 가능
  Py_INCREF(Py_NONE);
  return Py_None;
  ```

### Error/ Exception

* function fail: exception condition을 설정하고 에러값을 리턴한다.(보통 NULL값이다.)
* exception은 인터프리터 내부에 static global한 변수에 저장된다. NULL값이라면 예외가 발생하지 않는다. 또 다른 static global한 변수는 해당 exception과 연관된 값(*associated value*)를 저장한다. 세 번째 변수는 파이썬 코드에서 에러가 발생할 경우를 대비해 스택 traceback을 저장한다. 이 세 변수들은 파이썬에서 [`sys.exc_info()`](https://docs.python.org/3.5/library/sys.html#sys.exc_info)와 같다.
* 값 지정하기
  * `PyErr_SetSTring()` : *associated value*로 바꿔준다. 에러 원인 표시
  * `PyErr_setFromErrno()`: `errno`에서 
  * `PyErr_SetObject()`: general한 버전, 값과 객체를 모두 넘김
  * 중복 정의하면 안된다. 예를 들어 `f`함수가 `g`함수를 호출할 때 `f`와 `g` 모두 `PyErr_*`함수를 호출하면 에러가 난다.
  * `PyErr_Clear()`를 호출하면 파이썬 인터프리터로 예외를 넘기지 않고 C 모듈 내에서 처리해야 한다.
* 메모리 할당 실패시 **항상** `PyErr_NoMemory()`를 호출해야 한다. 다른 함수들은 (`PyLong_FromLong()`)은 알아서 지정한다.
* 새로운 예외를 만드는 것도 가능하다: `PyErr_NewException`
* NULL를 리턴하면 에러를 만드는 것과 같다.

### 모듈 메소드 테이블/ 초기화 함수

```c
static PyMethodDef SpamMethods[] = {
    ...
    {"system",  spam_system, METH_VARARGS,
     "Execute a shell command."},
    ...
    {NULL, NULL, 0, NULL}        /* Sentinel */
};
```

세번째 entry인 `METH_VARARGS`는 인터프리터에게 C함수의 호출 규약을 따를 것임을 알려주는 플래그이다. 보통은 `METH_VARARGS` or `METH_VARARGS | METH_KEYWORDS`를 사용한다.

* `METH_VARARGS` : 튜플 형태로 파라미터를 넘긴다.

* `METH_KEYWORDS`: 키워드와 같이 넘긴다.

모듈 메소드 테이블은 모듈 정의 구조체에 넘겨야 한다.

```c
static struct PyModuleDef spammodule = {
   PyModuleDef_HEAD_INIT,
   "spam",   /* name of module */
   spam_doc, /* module documentation, may be NULL */
   -1,       /* size of per-interpreter state of the module,
                or -1 if the module keeps state in global variables. */
   SpamMethods
};
```

이 구조체는 반드시 초기화 함수로 넘겨야 한다. 초기화 함수의 이름은 *반드시* `PyInit_이름()` 형태로 되어있어야 한다. 이 함수만 non-static이다.

```c
PyMODINIT_FUNC
PyInit_spam(void)
{
    return PyModule_Create(&spammodule);
}
```

### 컴파일/ 링킹

## 빌드

* C extension: 초기화 함수를 export하는 공유 라이브러리
* shared library가 만드시 `PYTHONPATH`에 위치해야 함

```c
PyObject* PyInit_modulename(void)
```

### `distutils` 이용하기

