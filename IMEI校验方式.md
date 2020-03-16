# 15位IMEI验证位验证方式

IMEI为TAC + FAC + SNR + SP。IMEI(International Mobile Equipment Identity)是国际移动设备身份码的缩写，国际移动装备辨识码，是由15位数字组成的”电子串号”，它与每台移动电话机一一对应，而且该码是全世界唯一的。每一只移动电话机在组装完成后都将被赋予一个全球唯一的一组号码，这个号码从生产到交付使用都将被制造生产的厂商所记录。

其组成为:

1.前6位数(TAC)是“型号核准号码”，一般代表机型。

2.接着的2位数(FAC)是”最后装配号”，一般代表产地。

3.之后的6位数(SNR)是“串号”，一般代表生产顺序号。

4.最后1位数(SP)通常是”0”，为检验码，目前暂备用。



IMEI最后一位是验证码，通过官方提供的验证方式就可以知道每一个imei是否为符合规则的imei号。

验证规则:

1.区分imei的奇数位和偶数位。
2.奇数位相加。
3.偶数为乘以2，若小于10则直接相加，大于10则对十位数和个位数进行相加。
4.奇数位相加之和与第3步逻辑只和相加，获取到一个数字。
5.得到的数字与10进行取余，余数若为0，则验证位数字为0，若余数不为0，则验证位为(10-余数)。



举例:IEMI为866696022549032的验证逻辑如下。

![img](https://pic4.zhimg.com/80/v2-09a5f3743accb0894515c72c183e0d03_hd.jpg)



获得最后的结果为58，58/10余8，则验证位为10-8=2。



验证代码如下:

```java
public static boolean isIMEI(String imei) {
    char[] imeiChar = imei.toCharArray();
    int resultInt = 0;
    for (int i = 0; i < imeiChar.length-1; i++) {
        int a = Integer.parseInt(String.valueOf(imeiChar[i]));
        i++;
        final int temp = Integer.parseInt(String.valueOf(imeiChar[i])) * 2;
        final int b = temp < 10 ? temp : temp - 9;
        resultInt += a + b;
    }
    resultInt %= 10;
    resultInt = resultInt == 0 ? 0 : 10 - resultInt;
    int crc= Integer.parseInt(String.valueOf(imeiChar[14]));
    return (resultInt == crc);
}
```

编辑于 2018-10-18