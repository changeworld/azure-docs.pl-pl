---
title: Jak skonfigurować RHEL/CentOS 7 - Usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować RHEL/CentOS 7, tak aby można było używać SDK mowy.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639164"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurowanie RHEL/CentOS 7 dla sdk mowy

Red Hat Enterprise Linux (RHEL) 8 x64 i CentOS 8 x64 są oficjalnie obsługiwane przez speech SDK w wersji 1.10.0 i nowszych. Jest również możliwe użycie zestawu SDK mowy na RHEL/CentOS 7 x64, ale wymaga to aktualizacji kompilatora Języka C++ (dla rozwoju języka C++ i udostępnionej biblioteki wykonawczej języka C++ w systemie.

Aby sprawdzić wersję kompilatora języka C++, uruchom:

```bash
g++ --version
```

Jeśli kompilator jest zainstalowany, dane wyjściowe powinny wyglądać następująco:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Ten komunikat informuje, że GCC wersja główna 4 jest zainstalowana. Ta wersja nie ma pełnej obsługi standardu C++ 11, którego używa pakiet SDK mowy. Próba skompilowania programu C++ z tą wersją GCC i nagłówkami SDK mowy spowoduje błędy kompilacji.

Ważne jest również, aby sprawdzić wersję udostępnionej biblioteki środowiska uruchomieniowego języka C++ (libstdc++). Większość speech SDK jest implementowana jako natywne biblioteki języka C++, co oznacza, że zależy od libstdc++ niezależnie od języka używanego do tworzenia aplikacji.

Aby znaleźć lokalizację libstdc++ w systemie, uruchom:

```bash
ldconfig -p | grep libstdc++
```

Wyjście na wanilii RHEL/CentOS 7 (x64) wynosi:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Na podstawie tego komunikatu należy sprawdzić definicje wersji za pomocą tego polecenia:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Wyjście powinno być:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

SDK mowy wymaga **CXXABI_1.3.9** i **GLIBCXX_3.4.21**. Informacje te można znaleźć, uruchamiając `ldd libMicrosoft.CognitiveServices.Speech.core.so` biblioteki zestawu SDK mowy z pakietu Linux.

> [!NOTE]
> Zaleca się, że wersja GCC zainstalowany w systemie jest co najmniej **5.4.0**, z pasującymi bibliotekami środowiska uruchomieniowego.

## <a name="example"></a>Przykład

Jest to przykładowe polecenie, które ilustruje sposób konfigurowania RHEL/CentOS 7 x64 do programowania (C++, C#, Java, Python) za pomocą zestawu SDK mowy 1.10.0 lub nowszego:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o zestawie SDK usługi Mowa](speech-sdk.md)
