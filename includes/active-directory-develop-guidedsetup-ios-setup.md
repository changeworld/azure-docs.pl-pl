## <a name="setting-up-your-ios-application"></a>Konfigurowanie aplikacji systemu iOS

Ta sekcja zawiera instrukcje krok po kroku dotyczące sposobu tworzenia nowego projektu aby zademonstrować sposób integracji aplikacji systemu iOS (Swift) z *logowania z firmą Microsoft* aby mogła zbadać interfejsów API sieci Web, które wymagają tokenu.

> Preferowane jest zamiast tego Pobierz ten przykład XCode projekt? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) i przejść [kroku konfiguracji](#register-your-application) skonfigurować przykładowy kod przed wykonaniem.


## <a name="install-carthage-to-download-and-build-msal"></a>Zainstaluj Carthage do pobrania i kompilacji MSAL
Menedżer pakietów Carthage jest używana w okresie Podgląd MSAL — integruje się z XCode przy zachowaniu możliwości dla firmy Microsoft wprowadzić zmiany w bibliotece.

- Pobierz i zainstaluj najnowszą wersję Carthage [tutaj](https://github.com/Carthage/Carthage/releases "Carthage adresu URL pobierania")

## <a name="creating-your-application"></a>Tworzenie aplikacji

1.  Otwórz środowisko Xcode i wybierz pozycję `Create a new Xcode project`
2.  Wybierz `iOS`  >  `Single view Application` i kliknij przycisk *dalej*
3.  Nadaj nazwę produktu, a następnie kliknij przycisk *dalej*
4.  Wybierz folder do utworzenia aplikacji, a następnie kliknij przycisk *Utwórz*

## <a name="build-the-msal-framework"></a>Tworzenie MSAL Framework

Postępuj zgodnie z instrukcjami poniżej, aby pobierać i późniejszego kompilowania najnowszej wersji biblioteki MSAL przy użyciu Carthage:

1.  Otwórz bash terminal i przejdź do folderu głównego aplikacji
2.  Kopiuj poniżej i Wklej w terminalu bash, aby utworzyć plik "Cartfile":

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Skopiuj i Wklej poniżej. To polecenie pobiera zależności do folderu Carthage/wyewidencjonowania, a następnie tworzy bibliotekę MSAL:
</li>
</ol>

```bash
carthage update
```

> Proces powyżej służy do pobierania i tworzenie biblioteki uwierzytelniania firmy Microsoft (MSAL). MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkownika, które umożliwiają dostęp do interfejsów API chronione przez usługi Azure Active Directory w wersji 2.

## <a name="add-the-msal-framework-to-your-application"></a>Dodaj platformę MSAL do aplikacji
1.  W środowisku Xcode Otwórz `General` kartę
2.  Przejdź do `Linked Frameworks and Libraries` sekcji, a następnie kliknij przycisk `+`
3.  Wybierz pozycję `Add other…`
4.  Wybierz: `Carthage`  >  `Build`  >  `iOS`  >  `MSAL.framework` i kliknij przycisk *Otwórz*. Powinny pojawić się `MSAL.framework` dodany do listy.
5.  Przejdź do `Build Phases` , a następnie kliknij pozycję `+` ikony, wybierz pozycję `New Run Script Phase`
6.  Dodaj następującą zawartość do *skryptu obszaru*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Dodaj następujący kod do <code>Input Files</code> klikając <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Tworzenie aplikacji interfejsu użytkownika
Plik Main.storyboard należy utworzyć automatycznie w ramach szablonu projektu. Postępuj zgodnie z instrukcjami poniżej, aby utworzyć aplikację interfejsu użytkownika:

1.  Kontrolowanie i kliknięcia `Main.storyboard` wyświetlić menu kontekstowe, a następnie kliknij przycisk: `Open As` > `Source Code`
2.  Zastąp `<scenes>` węzła przy użyciu poniższego kodu:

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
                            <state key="normal" title="Sign-Out"/>
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
