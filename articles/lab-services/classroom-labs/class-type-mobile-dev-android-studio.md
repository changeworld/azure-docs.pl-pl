---
title: Skonfiguruj laboratorium, aby uczyć tworzenia aplikacji mobilnych z Android Studio
titleSuffix: Azure Lab Services
description: Dowiedz się, jak skonfigurować laboratorium, aby uczyć klasy tworzenia aplikacji mobilnych danych, która korzysta z Android Studio.  Artykuł omówi również zmiany, które należy wprowadzić podczas korzystania z programu Android Studio na maszynie wirtualnej na platformie Azure.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 0c257589a2e93ac4c15a639e7156d0c0944b033c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849788"
---
# <a name="set-up-a-lab-to-teach-data-mobile-application-development-with-android-studio"></a>Skonfiguruj laboratorium, aby uczyć tworzenia aplikacji mobilnych danych z Android Studio

W tym artykule pokazano, jak skonfigurować wprowadzającą klasę tworzenia aplikacji mobilnych.  Ta klasa koncentruje się na aplikacjach mobilnych na Androida, które można opublikować w [Sklepie Google Play.](https://play.google.com/store/apps)  Uczniowie uczą się, jak używać [Android Studio](https://developer.android.com/studio) do tworzenia aplikacji.  [Visual Studio Emulator dla systemu Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) jest używany do testowania aplikacji lokalnie.

## <a name="lab-configuration"></a>Konfiguracja laboratorium

Aby skonfigurować to laboratorium, potrzebujesz subskrypcji platformy Azure i konta laboratorium, aby rozpocząć. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem. Po uzyskaniu subskrypcji platformy Azure możesz utworzyć nowe konto laboratorium w usłudze Azure Lab Services. Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [samouczek, aby skonfigurować konto laboratorium](tutorial-setup-lab-account.md).  Można również użyć istniejącego konta laboratorium.

Postępuj zgodnie z [samouczkiem laboratorium w klasie,](tutorial-setup-classroom-lab.md) aby utworzyć nowe laboratorium, a następnie zastosuj następujące ustawienia:

| Rozmiar maszyny wirtualnej | Image (Obraz) |
| -------------------- | ----- |
| Średni (wirtualizacja zagnieżdżona) | Windows Server 2019 Datacenter |

## <a name="template-machine-configuration"></a>Konfiguracja maszyny szablonu

Po zakończeniu tworzenia maszyny szablonu [uruchom komputer i połącz się z nim,](how-to-create-manage-template.md#update-a-template-vm) aby wykonać następujące zadania:

1. Dodawanie roli funkcji Hyper-V
2. Pobierz i zainstaluj javę.  
3. Pobierz i zainstaluj emulator programu Visual Studio dla systemu Android.
4. Pobierz i zainstaluj Android Studio.
5. Konfigurowanie emulatora programu Visual Studio dla programu Android Studio.

## <a name="add-hyper-v-role"></a>Dodawanie roli funkcji Hyper-V

Funkcja Hyper-V musi być włączona dla pomyślnej instalacji emulatora programu Visual Studio dla systemu Android.  Postępuj zgodnie z [instrukcjami, jak włączyć wirtualizacji zagnieżdżonej w szablonie maszyny wirtualnej](how-to-enable-nested-virtualization-template-vm.md) artykułu.

## <a name="install-java"></a>Instalowanie oprogramowania Java

Android Studio wymaga oprogramowania Java.  Wykonaj poniższe czynności, aby pobrać najnowszą wersję oprogramowania Java.

1. Przejdź do [strony pobierania języka Java](https://www.java.com/download/). Kliknij przycisk **Pobieranie w javie.**
2. Na 64-bitowej stronie internetowej systemu Windows for Java kliknij przycisk **Zgadzam się i rozpocznij bezpłatne pobieranie.**
3. Po **wyświetleniu instalatora instalatora oprogramowania Java** kliknij pozycję **Zainstaluj**.
4. Poczekaj, aż tytuł instalatora zmieni się na **Instalatora Java — Zakończ**.  Kliknij przycisk **Zamknij.**

## <a name="install-visual-studio-emulator-for-android"></a>Instalowanie emulatora programu Visual Studio dla systemu Android

Aby przetestować aplikację systemu Android lokalnie, musi użyć zwirtualizowanej wersji urządzenia z systemem Android.  Dostępnych jest kilka emulatorów systemu Android, które pozwolą deweloperowi przetestować ich aplikację z komputera.  Używamy Visual Studio Emulator dla systemu Android, ponieważ jest emulatorem, który obsługuje zagnieżdżonej wirtualizacji.  Ponieważ maszyna wirtualna usługi lab jest już maszyną wirtualną, potrzebujemy emulatora, który obsługuje wirtualizację zagnieżdżoną.  Wbudowany emulator dla programu Android Studio nie obsługuje zagnieżdżonej wirtualizacji.  Aby zobaczyć, które emulatory obsługują wirtualizację zagnieżdżoną, zobacz [akceleracja sprzętowa w celu uzyskania wydajności emulatora (Hyper-V & HAXM).](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/hardware-acceleration)

Poniższe instrukcje można pobrać i zainstalować emulator programu Visual Studio dla systemu Android.

1. Przejdź do [emulatora programu Visual Studio dla](https://visualstudio.microsoft.com/vs/msft-android-emulator/) strony głównej systemu Android.
2. Kliknij przycisk **Pobierz emulator.**
3. Po pobraniu pliku vs_emulatorsetup.exe uruchom plik wykonywalny.
4. Po wyświetleniu okna dialogowego ustawień programu Visual Studio kliknij przycisk **Zainstaluj.**
5. Poczekaj na zakończenie instalacji.  Kliknij przycisk **Uruchom ponownie,** aby ponownie uruchomić komputer i zakończyć instalację.

Uruchom emulator najpierw przed wdrożeniem aplikacji przy użyciu programu Android Studio.  Aby uzyskać więcej informacji na temat emulatora programu Visual Studio dla systemu Android, zobacz [Emulator programu Visual Studio dla dokumentacji systemu Android.](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-emulator-for-android)

## <a name="install-android-studio"></a>Zainstaluj Android Studio

Postępuj zgodnie z poniższymi instrukcjami, aby pobrać i zainstalować [Android Studio](https://developer.android.com/studio).

1. Przejdź do [strony pobierania Android Studio](https://developer.android.com/studio#downloads).  
    > [!NOTE]
    > Program Internet Explorer nie jest obsługiwany przez tę witrynę.
2. Kliknij pakiet wykonywalny systemu Windows (64-bitowy) android studio.
3. Przeczytaj warunki prawne napisane w wyskakującym okienku.  Gdy będzie gotowy do kontynuowania, zaznacz pole **wyboru Przeczytałem i zgadzam się z powyższymi warunkami** i kliknij przycisk **Pobierz Android Studio dla systemu Windows.**
4. Po pobraniu pliku wykonywalnego konfiguracji Android Studio uruchom plik wykonywalny.
5. Na stronie **Konfiguracja programu Android Studio w** programie Android Studio instalatora programu Android **Studio** kliknij przycisk **Dalej**.
6. Na stronie **Ustawienia konfiguracji** kliknij przycisk **Dalej**.
7. Na stronie **Wybierz folder menu Start** kliknij pozycję **Zainstaluj**.
8. Poczekaj na zakończenie konfiguracji.
9. Na stronie **Zakończenie instalacji** kliknij przycisk **Dalej**.
10. Na stronie **Kończenie instalacji programu Android Studio.**  Kliknij przycisk **Zakończ**.
11. Android Studio rozpocznie się automatycznie po zakończeniu konfiguracji.
12. W oknie dialogowym **Importowanie ustawień systemu Android z...** wybierz pozycję **Nie importuj ustawień**. Kliknij przycisk **OK**.
13. Na stronie **powitalnej** **Kreatora instalacji programu Android Studio**kliknij przycisk **Dalej**.
14. Na stronie **Typ instalacji** wybierz pozycję **Standardowy**. Kliknij przycisk **alej**.
15. Na stronie **Wybierz motyw interfejsu użytkownika** wybierz żądany motyw. Kliknij przycisk **alej**.
16. Na stronie **Weryfikuj ustawienia** kliknij przycisk **Dalej**.
17. Na stronie **Pobieranie składników** poczekaj, aż wszystkie składniki zostaną pobrane.  Kliknij przycisk **Zakończ**.

    > [!IMPORTANT]
    > Oczekuje się, że instalacja HAXM nie powiedzie się.  HAXM nie obsługuje wirtualizacji zagnieżdżonej, dlatego wcześniej w tym artykule zainstalowano emulator programu Visual Studio dla systemu Android.

18. Po zakończeniu pracy kreatora konfiguracji pojawi się okno dialogowe Witamy w u studio **systemu Android.**

## <a name="configure-android-studio-and-visual-studio-emulator-for-android"></a>Konfigurowanie emulatora programu Android Studio i programu Visual Studio dla systemu Android

Android Studio jest prawie gotowy do użycia.  Nadal musimy poinformować Visual Studio Emulator dla systemu Android, gdzie jest zainstalowany pakiet SDK systemu Android.  Spowoduje to, że wszystkie emulatory uruchomione w programie Visual Studio dla systemu Android są wyświetlane jako obiekty docelowe wdrażania dla debugowania programu Android Studio.

Musimy ustawić określony klucz rejestru, aby poinformować visual studio emulator dla systemu Android, gdzie znajduje się sdk systemu Android.  Aby ustawić potrzebny klucz rejestru, uruchom poniższy skrypt.  Poniższy skrypt programu PowerShell zakłada domyślną lokalizację instalacji dla systemu Android Sdk.  Jeśli zainstalowano pakiet Sdk systemu Android w `$androidSdkPath` innej lokalizacji, zmodyfikuj wartość przed uruchomieniem skryptu.

```powershell
$androidSdkPath = Resolve-Path $(Join-Path "$($env:APPDATA)" "../Local/Android/Sdk")

$registryKeyPath = "HKLM:Software\WOW6432NODE\Android Sdk Tools"
New-Item -Path $registryKeyPath
New-ItemProperty -Path $registryKeyPath -Name Path -PropertyType String -Value $androidSdkPath
```

> [!IMPORTANT]
> Uruchom ponownie visual studio emulator dla systemu Android i Android Studio, więc nowe ustawienie jest używany.

Uruchom wersję, której potrzebujesz w emulatorze programu Visual Studio.  Pojawi się jako miejsce docelowe wdrożenia aplikacji na Androida w ucies w u studio systemu Android.  Minimalna wersja dla projektu Programu Android Studio musi obsługiwać wersję uruchomię w emulatorze programu Visual Studio dla systemu Android.  Teraz możesz przystąpić do tworzenia i debugowania projektów przy użyciu programu Android Studio i emulatora programu Visual Studio dla systemu Android.  Jeśli masz jakiekolwiek problemy, zobacz Rozwiązywanie problemów z emulatorem systemu Android.

## <a name="cost"></a>Koszty

Jeśli chcesz oszacować koszt tego laboratorium, możesz postępować zgodnie z poniższym przykładem.
Dla klasy 25 uczniów z 20 godzinami zaplanowanego czasu zajęć i 10 godzinami przydziału na pracę domową lub zadania, cena za laboratorium  

25 \* studentów (20 zaplanowanych + 10 przydziałów) godzin * 55 jednostek laboratoryjnych * 0.01 USD za godzinę = 412.5 USD

Więcej informacji na temat cen można znaleźć w [cenniku usług Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne dla konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustawianie przydziału](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji wiadomości e-mail do studentów](how-to-configure-student-usage.md#send-invitations-to-users)
