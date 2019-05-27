---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: 1f4a1e289f19e53bda1804a170c0c09d4cf081ec
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967622"
---
## <a name="setting-up-your-ios-application"></a>Konfigurowanie aplikacji dla systemu iOS

Ta sekcja zawiera instrukcje krok po kroku dotyczące tworzenia nowego projektu pokazują, jak zintegrować aplikację systemu iOS (Swift) za pomocą *Zaloguj się przy użyciu Microsoft* , dzięki czemu może wysyłać zapytania interfejsów API sieci Web, które wymagają tokenu.

> Chcesz zamiast tego Pobierz ten przykładowy projekt XCode? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) , a następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu przed wykonaniem.

## <a name="install-carthage-to-download-and-build-msal"></a>Zainstaluj Carthage do pobierania i tworzenia biblioteki MSAL

Menedżer pakietów Carthage jest używana w okresie zapoznawczym MSAL — integruje się z narzędziem XCode przy zachowaniu możliwości firmy Microsoft, aby wprowadzić zmiany w bibliotece.

- Pobierz i zainstaluj najnowszą wersję Carthage [tutaj](https://github.com/Carthage/Carthage/releases "adresu URL pobierania Carthage").

## <a name="creating-your-application"></a>Tworzenie aplikacji

1. Otwórz program Xcode i wybierz **Utwórz nowy projekt Xcode**.
2. Wybierz **dla systemu iOS > pojedynczy widok aplikacji** i wybierz **dalej**.
3. Nadaj nazwę produktu, a następnie wybierz pozycję **dalej**.
4. Wybierz folder, aby utworzyć aplikację, a następnie kliknij przycisk *Create*

## <a name="build-the-msal-framework"></a>Tworzenie w ramach biblioteki MSAL

Postępuj zgodnie z poniższymi instrukcjami, aby ściągać i następnie skompilować najnowszą wersję biblioteki MSAL przy użyciu Carthage:

1. Otwórz terminal powłoki bash i przejdź do folderu głównego aplikacji.
2. Kopiuj poniżej i Wklej w terminalu powłoki bash, aby utworzyć plik "Cartfile":

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Skopiuj i Wklej poniżej. To polecenie pobiera zależności w folderze Carthage/wyewidencjonowania, a następnie tworzy biblioteki MSAL:
</li>
</ol>

```bash
carthage update
```

> Powyższy proces umożliwia pobieranie i tworzenie Microsoft Authentication Library (MSAL). Biblioteka MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkownika, które umożliwiają dostęp do interfejsów API chroniony przez platforma tożsamości firmy Microsoft.

## <a name="add-the-msal-framework-to-your-application"></a>Dodaj platformę biblioteki MSAL do swojej aplikacji

1. W środowisku Xcode Otwórz **ogólne** kartę.
2. Przejdź do **połączone struktury i biblioteki** i wybierz pozycję **+**.
3. Wybierz **dodać inne...** .
4. Wybierz **Carthage > kompilacji > iOS > MSAL.framework** , a następnie wybierz **Otwórz**. Powinien zostać wyświetlony `MSAL.framework` dodany do listy.
5. Przejdź do **fazy kompilacji** zaznacz **+** ikonę, a następnie wybierz **nowa faza Uruchom skrypt**.
6. Dodaj następującą zawartość do *skryptu obszaru*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Dodaj następujące polecenie, aby <code>Input Files</code> , klikając <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Tworzenie aplikacji interfejsu użytkownika

Plik Main.storyboard automatycznie powinien zostać utworzony jako część szablonu projektu. Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć aplikację interfejsu użytkownika:

1.  Control + kliknij `Main.storyboard` przywołać menu kontekstowe, a następnie kliknij przycisk: `Open As` > `Source Code`
2.  Zastąp `<scenes>` węzła z poniższym kodem:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
