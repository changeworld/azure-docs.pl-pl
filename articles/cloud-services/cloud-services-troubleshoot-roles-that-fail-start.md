---
title: Rozwiązywanie problemów z rolami, których nie można uruchomić | Dokumenty firmy Microsoft
description: Oto kilka typowych powodów, dla których uruchomienie roli usługi w chmurze może zakończyć się niepowodzeniem. Rozwiązania tych problemów są również dostarczane.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 674b2faf-26d7-4f54-99ea-a9e02ef0eb2f
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 869453d92f536a62aacc2be52598223158566ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71122728"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Rozwiązywanie problemów z rolami usługi w chmurze, których nie można uruchomić
Poniżej przedstawiono niektóre typowe problemy i rozwiązania związane z rolami usług Azure Cloud Services, których nie można uruchomić.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Brak bibliotek DLL lub zależności
Nieodpowiadające role i role, które są na rowerze między **inicjowania**, **Zajęty**, i **zatrzymania** stanów może być spowodowane przez brakujące biblioteki DLL lub zestawów.

Objawy brakujących bibliotek DLL lub zestawów mogą być:

* Wystąpienie roli jest przechodzenie na rowerze przez **inicjowanie**, **Zajęty**i **Zatrzymanie** stanów.
* Wystąpienie roli zostało przeniesione do **gotowości,** ale jeśli przejdziesz do aplikacji sieci web, strona nie zostanie wyświetlona.

Istnieje kilka zalecanych metod badania tych problemów.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnozowanie brakujących problemów z biblioteką DLL w roli sieci Web
Po przejściu do witryny sieci Web, która jest wdrażana w roli sieci web, a przeglądarka wyświetla błąd serwera podobny do następującego, może to oznaczać, że brakuje biblioteki DLL.

![Błąd serwera w aplikacji '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnozowanie problemów przez wyłączenie błędów niestandardowych
Pełniejsze informacje o błędach można wyświetlić, konfigurując web.config dla roli sieci web, aby ustawić niestandardowy tryb błędu na Wyłączone i ponownie wdrożyć usługę.

Aby wyświetlić pełniejsze błędy bez korzystania z pulpitu zdalnego:

1. Otwórz rozwiązanie w programie Microsoft Visual Studio.
2. W **Eksploratorze rozwiązań**znajdź plik web.config i otwórz go.
3. W pliku web.config znajdź sekcję system.web i dodaj następujący wiersz:

    ```xml
    <customErrors mode="Off" />
    ```
4. Zapisz plik.
5. Przepakuj i ponownie wdrożyć usługę.

Po ponowne wdrożenie usługi zostanie wyświetlony komunikat o błędzie o nazwie brakującego zestawu lub biblioteki DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnozowanie problemów przez zdalne wyświetlanie błędu
Pulpit zdalny umożliwia zdalny dostęp do roli i zdalne wyświetlanie bardziej kompletnych informacji o błędach. Aby wyświetlić błędy przy użyciu pulpitu zdalnego, należy wykonać następujące czynności:

1. Upewnij się, że jest zainstalowany zestaw SDK 1.3 lub nowszy platformy Azure.
2. Podczas wdrażania rozwiązania przy użyciu programu Visual Studio włącz pulpit zdalny. Aby uzyskać więcej informacji, zobacz [Włączanie połączenia pulpitu zdalnego dla roli w usługach w chmurze azure przy użyciu programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. W witrynie Microsoft Azure portal, gdy wystąpienie pokazuje stan **Ready**, zdalne do wystąpienia. Aby uzyskać więcej informacji na temat korzystania z pulpitu zdalnego za pomocą usług w chmurze, zobacz [Zdalne wystąpienia ról](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń, które zostały określone podczas konfiguracji pulpitu zdalnego.
6. Otwórz okno polecenia.
7. Wpisz polecenie `IPconfig`.
8. Zanotuj wartość adresu IPV4.
9. Otwórz program Internet Explorer.
10. Wpisz adres i nazwę aplikacji sieci web. Na przykład `http://<IPV4 Address>/default.aspx`.

Przejście do witryny sieci Web spowoduje teraz zwrócenie bardziej wyraźnych komunikatów o błędach:

* Błąd serwera w aplikacji '/'.
* Opis: wystąpił nieobsługiowany wyjątek podczas wykonywania bieżącego żądania sieci web. Przejrzyj ślad stosu, aby uzyskać więcej informacji na temat błędu i skąd pochodzi z kodu.
* Szczegóły wyjątku: System.IO.FIleNotFoundException: Nie można załadować pliku lub zestawu "Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35" lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku.

Przykład:

![Jawny błąd serwera w aplikacji '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnozowanie problemów przy użyciu emulatora obliczeniowego
Emulator obliczeń platformy Microsoft Azure służy do diagnozowania i rozwiązywania problemów z brakami zależności i błędów web.config.

Aby uzyskać najlepsze wyniki przy użyciu tej metody diagnozy, należy użyć komputera lub maszyny wirtualnej, która ma czystą instalację systemu Windows. Aby jak najlepiej symulować środowisko platformy Azure, użyj systemu Windows Server 2008 R2 x64.

1. Zainstaluj autonomiczną wersję pakietu [Azure SDK](https://azure.microsoft.com/downloads/).
2. Na komputerze deweloperskim skompiluj projekt usługi w chmurze.
3. W Eksploratorze Windows przejdź do folderu bin\debug projektu usługi w chmurze.
4. Skopiuj folder csx i plik cscfg na komputer, którego używasz do debugowania problemów.
5. Na czystym komputerze otwórz okno wiersza polecenia `csrun.exe /devstore:start`zestaw SDK platformy Azure i wpisz .
6. W wierszu polecenia `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`wpisz polecenie .
7. Po uruchomieniu roli w programie Internet Explorer zostaną wyświetleni szczegółowe informacje o błędzie. Można również użyć standardowych narzędzi do rozwiązywania problemów systemu Windows, aby jeszcze bardziej zdiagnozować problem.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnozowanie problemów przy użyciu funkcji IntelliTrace
W przypadku ról procesowych i sieci web korzystających z programu .NET Framework 4 można użyć funkcji [IntelliTrace](/visualstudio/debugger/intellitrace), która jest dostępna w programie Microsoft Visual Studio Enterprise.

Wykonaj następujące kroki, aby wdrożyć usługę z włączoną funkcją IntelliTrace:

1. Upewnij się, że jest zainstalowany zestaw SDK 1.3 lub nowszy platformy Azure.
2. Wdrażanie rozwiązania przy użyciu programu Visual Studio. Podczas wdrażania zaznacz pole wyboru **Włącz funkcję IntelliTrace dla ról .NET 4.**
3. Po uruchomieniu wystąpienia otwórz **Eksploratora serwera**.
4. Rozwiń węzeł **usług w chmurze Azure\\** i znajdź wdrożenie.
5. Rozwiń wdrożenie, dopóki nie zobaczysz wystąpień roli. Kliknij prawym przyciskiem myszy jedno z wystąpień.
6. Wybierz **pozycję Wyświetl dzienniki IntelliTrace**. Zostanie otwarte **podsumowanie IntelliTrace.**
7. Znajdź sekcję wyjątków podsumowania. Jeśli istnieją wyjątki, sekcja będzie oznaczona jako **Dane wyjątku**.
8. Rozwiń **dane wyjątku** i poszukaj błędów **System.IO.FileNotFoundException** podobnych do następujących:

![Dane wyjątku, brakujący plik lub zestaw](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Adres brakujących bibliotek DLL i zestawów
Aby rozwiązać brakujące błędy biblioteki DLL i zestawu, wykonaj następujące kroki:

1. Otwórz rozwiązanie w programie Visual Studio.
2. W **Eksploratorze rozwiązań**otwórz folder **Odwołania.**
3. Kliknij zestaw zidentyfikowany w błędzie.
4. W okienku **Właściwości** znajdź **właściwość Kopiuj lokalnie** i ustaw wartość **True**.
5. Ponowne wdrożenie usługi w chmurze.

Po sprawdzeniu, że wszystkie błędy zostały poprawione, można wdrożyć usługę bez zaznaczania pola wyboru **Włącz intellitrace dla ról .NET 4.**

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej [artykułów dotyczących rozwiązywania problemów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) z usługami w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy z rolami usługi w chmurze przy użyciu danych diagnostycznych komputera usługi Azure PaaS, zobacz [serię blogów Kevina Williamsona.](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)
