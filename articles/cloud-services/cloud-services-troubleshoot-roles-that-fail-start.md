---
title: Rozwiązywanie problemów z ról, które można uruchomić | Dokumentacja firmy Microsoft
description: Poniżej przedstawiono niektóre typowe przyczyny, dlaczego roli usługi w chmurze może się nie powieść. Podano także rozwiązania tych problemów.
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
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: d2daae2a3317d3b48748262d87ab8d7f7e13f2b0
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918420"
---
# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Rozwiązywanie problemów z ról usługi w chmurze, które można uruchomić
Poniżej przedstawiono niektóre typowe problemy i rozwiązania związane z usług Azure Cloud Services role, których nie udało się uruchomić.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>Brak bibliotek DLL lub zależności
Role nie odpowiada i role, które są cykliczne między **inicjowanie**, **zajęty**, i **zatrzymywanie** stany może być spowodowane przez Brak biblioteki DLL lub zestawów.

Objawy Brak biblioteki DLL lub zestawów może być:

* Twoje wystąpienie roli jest okrągło **inicjowanie**, **zajęty**, i **zatrzymywanie** stanów.
* Twoje wystąpienie roli została przeniesiona do **gotowe** , ale jeśli przejdziesz do aplikacji sieci web, strona nie jest wyświetlana.

Istnieje kilka metod zalecane do badania tych problemów.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnozowanie Brak problemów biblioteki DLL w roli sieci web
Po przejściu do witryny sieci Web, która jest wdrażana w sieci web roli i przeglądarka wyświetli błąd podobny do następującego serwera, może to oznaczać, że brakuje biblioteki DLL.

![Błąd serwera w aplikacji» /».](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnozowanie problemów, wyłączając błędów niestandardowych
Bardziej szczegółowe informacje o błędzie mogą zostać przejrzane przez konfigurowanie pliku web.config dla roli sieci web ustawić tryb błędu niestandardowego na Off i ponownie wdrożyć usługę.

Aby wyświetlić bardziej szczegółowy błędy bez przy użyciu pulpitu zdalnego:

1. Otwórz rozwiązanie w programie Microsoft Visual Studio.
2. W **Eksploratora rozwiązań**, Znajdź plik web.config i otwórz go.
3. W pliku web.config Odszukaj sekcję system.web i Dodaj następujący wiersz:

    ```xml
    <customErrors mode="Off" />
    ```
4. Zapisz plik.
5. Przeprowadź ponowne pakowanie i ponownie wdrożyć usługę.

Gdy usługa jest ponownie wdrażana, zobaczysz komunikat o błędzie z nazwą zestawu brakuje lub biblioteki DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnozowanie problemów, wyświetlając błąd zdalnie
Można użyć usług pulpitu zdalnego, dostęp do roli i zdalnie wyświetlić bardziej szczegółowe informacje o błędzie. Użyj następujących kroków, aby wyświetlić błędy przy użyciu pulpitu zdalnego:

1. Upewnij się, że zainstalowano zestaw SDK platformy Azure 1.3 lub nowszej.
2. Podczas wdrażania rozwiązania przy użyciu programu Visual Studio Włącz pulpit zdalny. Aby uzyskać więcej informacji, zobacz [Włącz Podłączanie pulpitu zdalnego dla roli w usługach Azure Cloud Services przy użyciu programu Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md).
3. W portalu Microsoft Azure, gdy wystąpienie pojawi się stan **gotowe**zdalne do wystąpienia. Aby uzyskać więcej informacji na temat korzystania z pulpitu zdalnego z usługami w chmurze, zobacz [zdalny do wystąpień roli](cloud-services-role-enable-remote-desktop-new-portal.md#remote-into-role-instances).
5. Zaloguj się do maszyny wirtualnej przy użyciu poświadczeń, które zostały określone podczas konfigurowania usług pulpitu zdalnego.
6. Otwórz okno polecenia.
7. Wpisz polecenie `IPconfig`.
8. Zanotuj wartość adresu IPV4.
9. Otwórz program Internet Explorer.
10. Wpisz adres i nazwę aplikacji sieci web. Na przykład `http://<IPV4 Address>/default.aspx`.

Przejdź do witryny sieci Web teraz zwróci dokładniejsze komunikaty o błędach:

* Błąd serwera w aplikacji» /».
* Opis: Wystąpił nieobsługiwany wyjątek podczas wykonywania bieżącego żądania sieci web. Sprawdź ślad stosu, aby uzyskać więcej informacji o błędzie i pochodzenie w kodzie.
* Szczegóły wyjątku: System.IO.FIleNotFoundException: Nie można załadować pliku lub zestawu ' Microsoft.WindowsAzure.StorageClient, wersja = 1.1.0.0, Culture = neutral, PublicKeyToken = 31bf856ad364e35 "lub jednej z jego zależności. W systemie nie można odnaleźć określonego pliku.

Na przykład:

![Błąd jawnego serwera w aplikacji» /»](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnozowanie problemów przy użyciu emulatora obliczeń
Emulator obliczeń Microsoft Azure można użyć do diagnozowania i rozwiązywania problemów brakujących zależności i błędy w pliku web.config.

Aby uzyskać najlepsze wyniki przy użyciu tej metody diagnostyki należy używać komputer lub maszynę wirtualną, która ma czystą instalację systemu Windows. Aby najlepiej symulowania środowiska platformy Azure, należy użyć systemu Windows Server 2008 R2 x64.

1. Zainstaluj wersję autonomicznej [zestawu Azure SDK](https://azure.microsoft.com/downloads/).
2. Na maszynie deweloperskiej Tworzenie projektu usługi w chmurze.
3. W Eksploratorze Windows przejdź do folderu bin\debug projekt usługi w chmurze.
4. Skopiuj plik csx folder i plik .cscfg do komputera, którego używasz do debugowania problemów.
5. Na czystej maszynie, Otwórz okno wiersza polecenia zestawu SDK platformy Azure i wpisz `csrun.exe /devstore:start`.
6. W wierszu polecenia wpisz `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.
7. Podczas uruchamiania roli, zobaczysz szczegółowe informacje o błędzie w programie Internet Explorer. Umożliwia także Windows standardowych narzędzi do rozwiązywania problemów można zdiagnozować problem.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnozowanie problemów przy użyciu funkcji IntelliTrace
W przypadku procesu roboczego i role sieci web, które używają .NET Framework 4, można użyć [IntelliTrace](/visualstudio/debugger/intellitrace), który jest dostępny w programie Microsoft Visual Studio Enterprise.

Wykonaj następujące kroki, aby wdrożyć usługę przy użyciu funkcji IntelliTrace jest włączony:

1. Upewnij się, że zainstalowano zestaw SDK platformy Azure 1.3 lub nowszej.
2. Wdrażanie rozwiązania przy użyciu programu Visual Studio. Podczas wdrażania, sprawdź **Włącz IntelliTrace dla ról platformy .NET 4** pole wyboru.
3. Po uruchomieniu wystąpienia Otwórz **Eksploratora serwera**.
4. Rozwiń **Azure\\usług w chmurze** węzła i Znajdź wdrożenie.
5. Rozwiń wdrożenia, aż zobaczysz wystąpień roli. Kliknij prawym przyciskiem myszy na jednym z wystąpień.
6. Wybierz **protokoly IntelliTrace widoku**. **Krótki opis IntelliTrace** zostanie otwarty.
7. Zlokalizuj sekcję wyjątki podsumowania. Jeśli istnieją wyjątki, zostaną oznaczone etykietą sekcji **dane o wyjątkach**.
8. Rozwiń **dane o wyjątkach** i poszukaj **System.IO.FileNotFoundException** błędy podobne do następującego:

![Dane wyjątku, Brak pliku lub zestawu](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Brak bibliotek DLL i zestawy adresów
Aby rozwiązać brakuje biblioteki DLL i błędów zestawu, wykonaj następujące kroki:

1. Otwórz rozwiązanie w programie Visual Studio.
2. W **Eksploratora rozwiązań**, otwórz **odwołania** folderu.
3. Kliknij zestaw identyfikowane w błędzie.
4. W **właściwości** okienku Znajdź **kopię lokalną właściwość** i ustaw wartość **True**.
5. Należy ponownie wdrożyć usługę w chmurze.

Po upewnieniu się, że wszystkie błędy zostały poprawione, możesz wdrożyć usługę bez sprawdzania **Włącz IntelliTrace dla ról platformy .NET 4** pole wyboru.

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej [artykuły dotyczące rozwiązywania problemów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) dla usług w chmurze.

Aby dowiedzieć się, jak rozwiązywać problemy dotyczące ról usługi chmury przy użyciu danych diagnostycznych na komputerze modelu PaaS platformy Azure, zobacz [serię wpisów w blogu Kevina Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
