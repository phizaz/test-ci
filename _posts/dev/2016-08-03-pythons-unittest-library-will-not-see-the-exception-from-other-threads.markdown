---
layout: post
title: Python's Unittest not seeing the exception from other threads
date: 2016-08-03 08:51:55.000000000 +07:00
categories: dev
tags: python
---
I don't know if using many threads for testing is a good practice or not. However, I do use it, and I do suffer the consequences from it.

Python's Unittest library *will not heed* whatever exception thrown from other threads than the main thread it running on!

This will definitely cause problems especially when exceptions happen and your tests are still quite *green*, that cannot be good.

One way to mitigate this problem is to create a *GlobalExceptionWatcher* that will see any exception thrown from any thread.

The implementation **I found over the internet** was kinda *hack*, yet using with cautions it worked!

It goes...
```
class GlobalExceptionWatcher(object):
    import threading
    import os

    '''
    NOTE: It changes the behavior of 'threading' module, which should be use in only limited area on a limited run
    '''

    def _store_excepthook(self):
        '''
        Uses as an exception handlers which stores any uncaught exceptions.
        '''
        formated_exc = self.__org_hook()
        self._exceptions.append(formated_exc)
        return formated_exc

    def start(self):
        '''
        Register us to the hook.
        '''
        self._exceptions = []
        self.__org_hook = self.threading._format_exc
        self.threading._format_exc = self._store_excepthook

    def stop(self):
        '''
        Remove us from the hook, assure no exception were thrown.
        '''
        self.threading._format_exc = self.__org_hook
        if len(self._exceptions) != 0:
            tracebacks = self.os.linesep.join(self._exceptions)
            raise Exception('Exceptions in other threads: %s' % tracebacks)

    def __enter__(self):
        self.start()

    def __exit__(self, type, value, traceback):
        self.stop()
```

You can now use:

```
class ExceptionOnThreadTest(unittest.TestCase):
    def test_exception_on_thread(self):
        import threading

        def run():
            import time
            time.sleep(0.1)
            raise Exception('just an exception')

        def watcher():
            with GlobalExceptionWatcher():
                t = threading.Thread(target=run)
                t.start()
                t.join()

        self.assertRaises(Exception, watcher)
```

And it turned *red* beautifully...
