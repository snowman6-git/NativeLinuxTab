## Project NLT
Real linux on the tablet nativly!

준비물
루팅/부트로더 언락 정도는 할 지식, 기초 리눅스 명령어, 굳이 힘든길로 가는 낭만☆

테스트 대상
|이름|CPU|RAM|가격|
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|---|------|
|Redmi SE|[Snapdragon 680 2.4GHZ](https://www.qualcomm.com/products/mobile/snapdragon/smartphones/snapdragon-6-series-mobile-platforms/snapdragon-680-4g-mobile-platform)|4GB|19만원|

사용 Linux
|OS|소스|
|----|:---:|
|[Archlinux ARM](https://archlinuxarm.org/platforms/armv8/generic)|[tar](http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz)|
```
curl -OL http://os.archlinuxarm.org/os/ArchLinuxARM-aarch64-latest.tar.gz
```
주요 참고 문서
|게시글|게시일|참고사유|중요도|
|---|---|---|:---:|
|[Arch Linux LXDE Mouse Keyboard on Android Tablet](https://thomaspolasek.blogspot.com/2012/04/arch-linux-lxde-w-xorg-mouse-keyboard_16.html)|April 16, 2012|SD카드를 통한 rootfs형식 리눅스 마운트|*****|
|[How to Root any Custom ROM via Magisk](https://droidwin.com/how-to-root-any-custom-rom-via-magisk/#Root_any_Custom_ROM_via_Magisk_Patched_Boot)|May 4, 2023|실수로 커롬질해서 TWRP를 못쓰게 됐을시 루팅법|***|
|[Xiaomi_Kernel_OpenSource](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/xun-t-oss)|Feb 4, 2020|기종별 샤오미 커널 오픈소스 현재 잘 못써서 2점|**|
|[Boot Android from SdCard](https://linux-sunxi.org/Boot_Android_from_SdCard)|Dec 18, 2017|boot.img 부트레코드/파라미터 추출/복제/빌드|?|

#### 왜 Archlinux였는가?
<li/> AUR, 최신패키지 지원 ex.(neovim 1.10.0)
<li/> 든게 없어서 가볍고 커스텀하기 좋음
<li/> 로고가 이쁨(제일중요)

#### 왜 에뮬레이터를 사용하지 않는가?
<li/> 안드로이드 기본 os의 높은 메모리 사용량 (gui)
<li/> 더 긴 시간 사용가능한 디바이스의 필요성
<li/> 리눅스 올라간 태블릿 너무 이쁨.(진짜중요)

### 밤새다 기절하더라도 잊으면 안돼는것들 -계속 추가할것.
<li/> tar안에 있는 리눅스 틀 그대로 아무커널에 올려서 sh arch/bin/bash를 사용하면 그때부터 archlinux가 됀다. 이것이 리눅스가 os가 아닌 커널이라 불리우는 이유이다.

단계
1. 루팅으로 권한휙득
2. magisk에서 adb shell 에 su 권한 부여
3. sd카드를 마운트하고 아래의 명령어를 실행

```bash
mkdir -p /mnt/aa2
mount /dev/block/mmcblk1p1 /mnt/aa2
export arch=/mnt/aa2
mount -o bind /dev $arch/dev
mount -t devpts devpts $arch/dev/pts
mount -t proc none $arch/proc
mount -t sysfs sysfs $arch/sys

export PATH=/sbin:/bin:/usr/bin:/usr/sbin:/usr/local/bin:$PATH
export TERM=xterm
export HOME=/root
export USER=root

chroot $arch /bin/bash
```
