# scd4x driver

linux source tree に含まれている scd4x.c を、
測定値をリードする間隔を5秒以上空けるように修正する。

CO2、温度、湿度のいずれかをを連続でリードしようとすると待たされる。
SCD4xは、CO2・温度・湿度を1回のレジスタリードで読み出す。読み出した直後は一定時間、新しい測定値が利用可能にならないため、最大6秒間のウェイトが発生する。

sysfsを通じてCO2・温度・湿度をそれぞれリードすると、毎回測定値のリードを行っていたため、毎回ウェイトが発生する。

## How to build

```
make DTC=/usr/bin/dtc
sudo make install
sudo depmod -A
sudo cp i2c-scd4x.dtbo /boot/firmware/overlays/
```

linux-headers パッケージには、scripts/dtc が含まれていないので dtc コマンドのフルパスを DTC 変数で指定する必要があります。
ビルド済みの linux source tree を KDIR 変数で指定する場合は不要です。

source tree の外で modules_install した場合、
> Warning: modules_install: missing 'System.map' file. Skipping depmod.

となり、depmod が実行されないので手動で depmod する。

## Example

```
dtoverlay=i2c-scd4x,scd40
```
