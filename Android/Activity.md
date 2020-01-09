### Activity

***

#### 1.activity的生命周期

首先给出Activity的完整生命周期，如下图所示：![Android生命周期图](C:\Users\Administrator\Desktop\demo\12239413-1abcc02af12743e4.png)

生命周期几种常见情况分析：

1.a页面跳转到b页面

|         情况         |                           生命周期                           |
| :------------------: | :----------------------------------------------------------: |
|        启动a         |              a_onCreate 、a_onStart、a_onResume              |
|  从a跳转到不透明的b  |    a_onPause、b_onCreate、b_onStart、b_onResume、a_onStop    |
|   从a跳转到透明的b   |         a_onPause、b_onCreate、b_onStart、b_onResume         |
| 从不透明的b再次回到a | b_onPause、a_onRestart、a_onStart、a_onResume、b_onStop、b_onDestory |
|  从透明的b再次回到a  |         b_onPause、a_onResume、b_onStop、b_onDestory         |
|      按 home 键      |                     a_onPause、a_onStop                      |
|    从后台返回到a     |                    a_onRestart、a_onStart                    |
|     按back退出a      |               a_onPause、a_onStop、a_onDestory               |



2.单页面屏幕旋转生命周期变化



|                        情况                         |                            切横屏                            |                            切竖屏                            |                             结论                             |
| :-------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|       不设置Activity的android:configChanges时       | onPause、onStop、onDestroy、onCreate、onStart、onRestoreInstanceState、onResume | onPause、onStop、onDestroy、onCreate、onStart、onRestoreInstanceState、onResume | 切屏会重新调用各个生命周期，切横屏时会执行1次，切竖屏时会执行1次 |
| 设置Activity的android:configChanges="orientation"时 | onPause、onStop、onDestroy、onCreate、onStart、onRestoreInstanceState、onResume |                                                              |                                                              |
|                                                     |                                                              |                                                              |                                                              |

