ESERAMair用ドライバ単体を置いています。

以下のコマンドでNextor kernelと結合することで、nextor-eseramair.rom を生成することができます。

```
mknexrom Nextor-2.1.3.base.dat nextor-eseramair.rom /m:chgbnk.bin /d:driver.bin /8:6000h
```