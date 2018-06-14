---
title: Rozwiązywanie problemów z ról, które się nie uruchomić | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono niektóre typowe przyczyny, dlaczego roli usługi w chmurze może się nie uruchomić. Podawane są również rozwiązania tych problemów.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 36966151b616a14e429807feb63df93503e7b249
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29875338"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Rozwiązywanie problemów z ról usługi w chmurze, które się nie uruchomić
Poniżej przedstawiono niektóre typowe problemy i rozwiązania związane z usług Azure Cloud Services ról, które się nie uruchomić.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Brak biblioteki DLL lub zależności
Odpowiadać oraz ról, które są cykliczne między **inicjowanie**, **zajęty**, i **zatrzymywanie** stanów może być spowodowany brakiem biblioteki DLL lub zestawów.

Objawy Brak biblioteki DLL lub zestawy mogą być:

* Wystąpienie roli jest okrągło **inicjowanie**, **zajęty**, i **zatrzymywanie** stanów.
* Wystąpienie roli została przeniesiona do **gotowe** , ale w przypadku przejścia do aplikacji sieci web nie ma strony.

Istnieje kilka metod zalecane do badania tych problemów.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnozowanie Brak problemów biblioteki DLL w roli sieć web
Po przejściu do witryny sieci Web, które zostało wdrożone w sieci web roli, przeglądarka wyświetli błąd podobny do następującego serwera, może oznaczać, że brakuje biblioteki DLL.

![Błąd serwera w "/" aplikacji.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnozowanie problemów przez wyłączenie błędów niestandardowych
Więcej informacji o można wyświetlić, konfigurując pliku web.config dla roli sieci web ustawić tryb błędów niestandardowych na Off i ponownie wdrożyć usługę.

Aby wyświetlić bardziej szczegółowy błędy bez przy użyciu pulpitu zdalnego:

1. Otwórz rozwiązanie w programie Microsoft Visual Studio.
2. W **Eksploratora rozwiązań**, Znajdź plik web.config i otwórz go.
3. W pliku web.config Znajdź sekcję system.web i Dodaj następujący wiersz:

    ```xml
    <customErrors mode="Off" />
    ```
4. Zapisz plik.
5. Spakuj ponownie i ponownie wdrożyć usługę.

Gdy ponownie wdrożyć usługę, zostanie wyświetlony komunikat o błędzie, o nazwie brakuje zestawu lub biblioteki DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnozowanie problemów poprzez wyświetlenie błędu zdalnie
Pulpit zdalny umożliwia dostęp roli i wyświetlać informacje o błędzie bardziej szczegółowy zdalnie. Wykonaj następujące kroki, aby wyświetlić błędy przy użyciu pulpitu zdalnego:

1. Upewnij się, że Azure SDK 1.3 lub nowszy jest zainstalowany.
2. Podczas wdrażania rozwiązania przy użyciu programu Visual Studio włączenie pulpitu zdalnego. Aby uzyskać więcej informacji, zobacz [włączyć Podłączanie pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. W portalu Microsoft Azure, gdy wystąpienie wskazuje stan **gotowe**nawiąż połączenie zdalne wystąpienie. Aby uzyskać więcej informacji na temat używania pulpitu zdalnego z usługami w chmurze, zobacz [zdalnego do wystąpień roli](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń, które zostały określone podczas konfigurowania usług pulpitu zdalnego.
6. Otwórz okno poleceń.
7. Wpisz polecenie `IPconfig`.
8. Zanotuj wartość adres IPV4.
9. Otwórz program Internet Explorer.
10. Wpisz adres i nazwę aplikacji sieci web. Na przykład `http://<IPV4 Address>/default.aspx`.

Przejście do witryny sieci Web zwróci teraz dokładniejsze komunikaty o błędach:

* Błąd serwera w "/" aplikacji.
* Opis: Wystąpił nieobsługiwany wyjątek podczas wykonywania bieżącego żądania sieci web. Przejrzyj ślad stosu, aby uzyskać więcej informacji o błędzie i miejscu jego występowania w kodzie.
* Szczegóły wyjątku: System.IO.FIleNotFoundException: nie można załadować pliku lub zestawu "Microsoft.WindowsAzure.StorageClient, wersja = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35" lub jednej z jego zależności. System nie może odnaleźć określonego pliku.

Na przykład:

![Błąd serwera jawne w '/' aplikacji](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnozowanie problemów przy użyciu emulatora obliczeń
Emulator obliczeń Microsoft Azure umożliwia diagnozowanie i rozwiązywanie problemów z brakujących zależności i błędy w pliku web.config.

Aby uzyskać najlepsze wyniki za pomocą tej metody diagnostyki należy używać komputer lub maszynę wirtualną, która ma czystą instalację systemu Windows. Aby najlepiej symulowania środowiska platformy Azure, należy użyć systemu Windows Server 2008 R2 x64.

1. Zainstaluj wersję autonomicznej [zestawu Azure SDK](https://azure.microsoft.com/downloads/).
2. Na komputerze deweloperskim należy utworzyć projekt usługi w chmurze.
3. W Eksploratorze Windows przejdź do folderu bin\debug projekt usługi w chmurze.
4. Skopiuj plik csx folder i .cscfg do komputera, którego używasz do debugowania problemów.
5. Na komputerze czystą, Otwórz okno wiersza polecenia Azure SDK i wpisz `csrun.exe /devstore:start`.
6. W wierszu polecenia wpisz `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Po uruchomieniu roli, zobaczysz szczegółowe informacje o błędzie w programie Internet Explorer. Aby zdiagnozować problem umożliwia także standardowego systemu Windows, narzędzia do rozwiązywania problemów.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnozowanie problemów przy użyciu funkcji IntelliTrace
Dla procesu roboczego oraz role sieci web, które używają programu .NET Framework 4, możesz użyć [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), która jest dostępna w programie Microsoft Visual Studio Enterprise.

Wykonaj następujące kroki, aby wdrożyć usługę przy użyciu funkcji IntelliTrace włączone:

1. Potwierdź, że zestaw Azure SDK 1.3 lub nowszej jest zainstalowane.
2. Wdrażanie rozwiązania przy użyciu programu Visual Studio. Podczas wdrażania, sprawdź **włączenia funkcji IntelliTrace dla ról platformy .NET 4** pole wyboru.
3. Po uruchomieniu wystąpienia Otwórz **Eksploratora serwera**.
4. Rozwiń węzeł **Azure\\usługi w chmurze** węzła i Znajdź wdrożenie.
5. Rozwiń węzeł wdrożenia, aż zostanie wyświetlony wystąpień roli. Kliknij prawym przyciskiem myszy na jedno wystąpienie.
6. Wybierz **dzienniki IntelliTrace widoku**. **Podsumowanie funkcji IntelliTrace** zostanie otwarty.
7. Znajdź sekcję wyjątki podsumowania. Jeśli istnieją wyjątki, zostaną oznaczone etykietą sekcji **wyjątku**.
8. Rozwiń węzeł **wyjątku** i poszukaj **System.IO.FileNotFoundException** błędy podobne do następującego:

![Dane wyjątku, Brak pliku lub zestawu](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Brak biblioteki dll i zestawy adresów
W celu rozwiązania Brak biblioteki DLL i zestawu błędów, wykonaj następujące kroki:

1. Otwórz rozwiązanie w programie Visual Studio.
2. W **Eksploratora rozwiązań**, otwórz **odwołania** folderu.
3. Kliknij zestaw zidentyfikowany w dzienniku błędów.
4. W **właściwości** okienku Znajdź **właściwości kopii lokalnej** i ustaw wartość **True**.
5. Należy ponownie wdrożyć usługę w chmurze.

Po upewnieniu się, że wszystkie błędy zostaną naprawione, można wdrożyć usługę bez sprawdzania **włączenia funkcji IntelliTrace dla ról platformy .NET 4** pole wyboru.

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej [Rozwiązywanie problemów z artykułów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy roli usługi w chmurze przy użyciu danych diagnostycznych na komputerze Azure PaaS, zobacz [serii blogu Kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
