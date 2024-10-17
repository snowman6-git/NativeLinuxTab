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

======================================================

긴 시간이 지나고 아직도 문제를 해결하지 못했다, shell환경에서 rootfs 를 진입하는건 간단했지만,

결국 아치리눅스로 부팅하지 못하면 의미가 없다. 그런 이유로 좀 더 부팅환경과 부트로더를 수정하기 편한 arm 디바이스를 먼저 실험해보는걸로 했다.

대상: Khadas Vim4

rEFind세팅에 따르면 부팅에 필요한 이미지들은 아래와 같다.

vmlinuz-linux, initramfs-?.img/fallback

하지만 archarm의 rootfs 에서는 vmlinuz가 없다. 이를 대체하는 Image.gz가 있음을 확인했다.

rootfs를 mnt에 마운트해두고 실행할것

```
export arch=/mnt
mount -o bind /dev $arch/dev
mount -t devpts devpts $arch/dev/pts
mount -t proc none $arch/proc
mount -t sysfs sysfs $arch/sys
```
chroot.sh라는 이름으로 pre에 있음

chroot 진입법, 만약 도메인 관련 에러가 나면 os 전체를 교체할것, 이미 클라우드에 있는게 손상됐을수있음

이번에도 역시나 부트로더를 편집해야할 필요가 있음, 우분투 설치 -> 아치 덮어쓰기를 시도해보았으나 램디스크의 이름이나 기타 등등의 이유로 부트실패 *추측

여하튼 부트로더를 편집할수있어야함

여러 문제 해결법

|문제|해결법|
|:-----:|:---:|
|도메인에 핑이 안가요(pacman)|부트이미지를 만들기 위한 파라미터가 있음|

======================================================

유용한 명령어들
|명령어|용도|
|-----|---|
|cat /proc/cmdline|부트이미지를 만들기 위한 파라미터가 있음|

주요 참고 문서
|게시글|게시일|참고사유|중요도|
|---|---|---|:---:|
|[Arch Linux LXDE Mouse Keyboard on Android Tablet](https://thomaspolasek.blogspot.com/2012/04/arch-linux-lxde-w-xorg-mouse-keyboard_16.html)|April 16, 2012|SD카드를 통한 rootfs형식 리눅스 마운트|*****|
|[How to Root any Custom ROM via Magisk](https://droidwin.com/how-to-root-any-custom-rom-via-magisk/#Root_any_Custom_ROM_via_Magisk_Patched_Boot)|May 4, 2023|실수로 커롬질해서 TWRP를 못쓰게 됐을시 루팅법|***|
|[Xiaomi_Kernel_OpenSource](https://github.com/MiCode/Xiaomi_Kernel_OpenSource/tree/xun-t-oss)|Feb 4, 2020|기종별 샤오미 커널 오픈소스 현재 잘 못써서 2점|**|
|[Boot Android from SdCard](https://linux-sunxi.org/Boot_Android_from_SdCard)|Dec 18, 2017|boot.img 부트레코드/파라미터 추출/복제/빌드|?|
|[최소한의 구성으로 부팅 가능한 이미지 만들어보기](https://unknownpgr.com/posts/make-bootable-disk/index.html)|2023-12-05|부트이미지를 만들기 위한 최소요구|?|
|[안드로이드 부팅로고 바꾸기](https://blog.naver.com/kangyunmoon/220731939104)|2016-6-9|커스텀을 더 하고 싶을때|*|

#### 왜 Archlinux였는가?
<li/> AUR, 최신패키지 지원 ex.(neovim 1.10.0)
<li/> 든게 없어서 가볍고 커스텀하기 좋음
<li/> 로고가 이쁨(제일중요)

#### 왜 에뮬레이터를 사용하지 않는가?
<li/> 안드로이드 기본 os의 높은 메모리 사용량 (gui)
<li/> 더 긴 시간 사용가능한 디바이스의 필요성
<li/> 리눅스 올라간 태블릿 너무 이쁨.(진짜중요)

### 밤새다 기절하더라도 잊으면 안돼는것들 -계속 추가할것.
<li/> sh가 사용가능한 환경만 되면 언제든지 archlinux가 사용이 가능한 환경이 된다, 이전 글에서는 kernal에 sh가 기본 내장이라는 오류가 있었다.
  
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

boot.img가 되기 위해 필요한것들
1. 커널 이미지 image.gz/ Image
2. RAMDISK initramfs.img
3. root지정(SD카드의 경우)

전부 archarm에 있으니 잘 조합해볼것
