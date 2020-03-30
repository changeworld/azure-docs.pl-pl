---
title: Dowiedz się więcej o najnowszych wersjach systemu operacyjnego dla gości platformy Azure | Dokumenty firmy Microsoft
description: Najnowsze wiadomości o wersji i zgodność sdk dla systemu operacyjnego gościa usług w chmurze azure.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2020
ms.author: raiye
ms.openlocfilehash: f265faef4e6520f5c74fce9a13b3d81118707025
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371122"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Wersje systemu operacyjnego gościa platformy Azure i macierz zgodności zestawów SDK
Zawiera aktualne informacje o najnowszych wersjach systemu operacyjnego azure dla gości dla usług w chmurze. Te informacje ułatwią planowanie ścieżki uaktualniania przed wyłączeniem systemu operacyjnego gościa. Jeśli skonfigurujesz swoje role do używania *automatycznych* aktualizacji systemu operacyjnego gościa zgodnie z opisem w [ustawieniach aktualizacji systemu operacyjnego gościa platformy Azure,][Azure Guest OS Update Settings]nie jest ważne, aby przeczytać tę stronę.

> [!IMPORTANT]
> Ta strona dotyczy ról sieci web i procesu roboczego usług w chmurze, które są uruchamiane na szczycie systemu operacyjnego gościa. Nie **dotyczy** maszyn wirtualnych IaaS.
>
>


> [!TIP]
>  Zapisz się do [źródła RSS aktualizacji systemu operacyjnego gościa,] aby otrzymywać najbardziej aktualne powiadomienie o wszystkich zmianach systemu operacyjnego gościa.
>
>

> [!IMPORTANT]
> Tylko najnowsze 2 wersje systemu operacyjnego gościa będą obsługiwane i dostępne w witrynie Azure portal.
>
>

Nie masz pewności, jak zaktualizować system operacyjny dla gości? Sprawdź [to.][cloud updates]

## <a name="news-updates"></a>Aktualności

###### <a name="march-5-2020"></a>**5 marca 2020 r.**
Lutowy system operacyjny gościa został wydany. 

###### <a name="january-24-2020"></a>**24 stycznia 2020 r.**
Styczniowy system operacyjny gościa został wydany. 

###### <a name="january-8-2020"></a>**8 stycznia 2020 r.**
Grudniowy system operacyjny gościa został wydany.

###### <a name="december-5-2019"></a>**5 grudnia 2019 r.**
Listopadowy system operacyjny gościa został wydany.

###### <a name="november-1-2019"></a>**1 listopada 2019 r.**
Październikowy system operacyjny gościa został wydany.

###### <a name="october-7-2019"></a>**7 października 2019 r.**
Wrześniowy system operacyjny gościa został wydany.

###### <a name="september-4-2019"></a>**4 września 2019 r.**
Sierpniowy system operacyjny gościa został wydany.

###### <a name="july-26-2019"></a>**26 lipca 2019 r.**
Lipcowy system operacyjny gościa został wydany.

###### <a name="july-8-2019"></a>**8 lipca 2019 r.**
Czerwcowy system operacyjny gościa został wydany.

###### <a name="june-6-2019"></a>**6 czerwca 2019 r.**
May Guest OS został wydany.

###### <a name="may-7-2019"></a>**7 maja 2019 r.**
April Guest OS wydała.

###### <a name="march-26-2019"></a>**26 marca 2019 r.**
Marcowy system operacyjny gościa został wydany.

###### <a name="march-12-2019"></a>**12 marca 2019 r.**
Lutowy system operacyjny gościa został wydany.

###### <a name="february-5-2019"></a>**5 lutego 2019 r.**
Styczniowy system operacyjny gościa został wydany.

###### <a name="january-24-2019"></a>**24 stycznia 2019 r.**
Family 6 Guest OS (Windows Server 2019) został wydany.

###### <a name="january-7-2019"></a>**7 stycznia 2019 r.**
Grudniowy system operacyjny gościa został wydany.

###### <a name="december-14-2018"></a>**14 grudnia 2018 r.**
Listopadowy system operacyjny gościa został wydany.

###### <a name="november-8-2018"></a>**8 listopada 2018 r.**
Październikowy system operacyjny gościa został wydany.

###### <a name="october-12-2018"></a>**12 października 2018 r.**
Wrześniowy system operacyjny gościa został wydany.

## <a name="releases"></a>Wydania

## <a name="family-6-releases"></a>Rodzina 6 zwalnia
**Windows Server 2019**

Zainstalowano program .NET Framework: 3.5, 4.7.2

> [!NOTE]
> Zestaw Windows Azure SDK dla platformy .NET — 3.0 można pobrać [tutaj][Windows Azure SDK].
>
>Kroki instalacji:
>1. Odinstaluj starsze wersje programu MicrosoftAzureAuthoringTools*.msi
>2. Instalowanie pakietu [Azure SDK dla platformy .NET — 3.0][Windows Azure SDK]
>3. Ponowne uruchomienie komputera
>4. Tworzenie nowego projektu usługi w chmurze i dodawanie jednej roli procesu roboczego
>5. Zmień rodzinę systemu operacyjnego na 6 i skompiluj pakiet
>6. Wdrażanie pakietu na platformie Azure przy użyciu portalu Azure lub programu Visual Studio
>
>Wersja systemu operacyjnego gościa Family 6 wymusza tls 1.2, jawnie wyłączając TLS 1.0 i 1.1 i definiując określony zestaw mechanizmów szyfrowania. Dowiedz się [więcej].


| Ciąg konfiguracji | Data wydania | Wyłącz datę |
| --- | --- | --- |
|  WA-GUEST-OS-6.16_202002-01  |  5 marca 2020 r.  |  Po 6.18  |  
|  WA-GUEST-OS-6.15_202001-01  |  24 stycznia 2020 r.  |  Po 6.17  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 8 stycznia 2020 r. | 5 marca 2020 r. |  
|~~WA-GUEST-OS-6.13_201911-01~~| 5 grudnia 2019 r. | 24 stycznia 2020 r. |  
|~~WA-GUEST-OS-6.12_201910-01~~| 1 listopada 2019 r. | 8 stycznia 2020 r. |  
|~~WA-GUEST-OS-6.11_201909-01~~| 7 października 2019 r. | 5 grudnia 2019 r. |  
|~~WA-GUEST-OS-6.10_201908-01~~| 4 sierpnia 2019 r. | 1 listopada 2019 r.  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26 lipca 2019 r. | 7 października 2019 r. |
|~~WA-GUEST-OS-6.8_201906-01~~|8 lipca 2019 r. |4 sierpnia 2019 r. |
|~~WA-GUEST-OS-6.7_201905-01~~ |6 czerwca 2019 r. |26 lipca 2019 r. |
|~~WA-GUEST-OS-6.6_201904-01~~ |7 maja 2019 r. |8 lipca 2019 r. |
|~~WA-GUEST-OS-6.5_201903-01~~ |26 marca 2019 r. |6 czerwca 2019 r. |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 stycznia 2019 r. |5 lutego 2019 r. |

## <a name="family-5-releases"></a>Rodzina 5 zwalnia
**Windows Server 2016**

Zainstalowano program .NET Framework: 3.5, 4.6

> [!NOTE]
> Hasło RDP dla rodziny systemu operacyjnego 5 musi mieć co najmniej 10 znaków.
>


| Ciąg konfiguracji | Data wydania | Wyłącz datę |
| --- | --- | --- |
|  WA-GUEST-OS-5.40_202002-01  |  5 marca 2020 r.  |  Post 5.42  |  
|  WA-GUEST-OS-5.39_202001-01  |  24 stycznia 2020 r.  |  Po 5.41  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 8 stycznia 2020 r. | 5 marca 2020 r. |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5 grudnia 2019 r. | 24 stycznia 2020 r. |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1 listopada 2019 r. | 8 stycznia 2020 r. |  
|~~WA-GUEST-OS-5.35_201909-01~~| 7 października 2019 r. | 5 grudnia 2019 r. |  
|~~WA-GUEST-OS-5.34_201908-01~~|  4 sierpnia 2019 r.  | 1 listopada 2019 r. |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26 lipca 2019 r. | 7 października 2019 r. |  
|~~WA-GUEST-OS-5.32_201906-01~~|8 lipca 2019 r. |4 sierpnia 2019 r. |
|~~WA-GUEST-OS-5.31_201905-01~~ |6 czerwca 2019 r. |26 lipca 2019 r. |
|~~WA-GUEST-OS-5.30_201904-01~~ |7 maja 2019 r. |8 lipca 2019 r. |
|~~WA-GUEST-OS-5.29_201903-01~~ |26 marca 2019 r. |6 czerwca 2019 r. |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-4-releases"></a>Rodzina 4 zwalnia
**Windows Server 2012 R2**

Zainstalowano program .NET Framework: 3.5, 4.5.1, 4.5.2

| Ciąg konfiguracji | Data wydania | Wyłącz datę |
| --- | --- | --- |
|  WA-GUEST-OS-4.75_202002-01  |  5 marca 2020 r.  |  Post 4.77  |  
|  WA-GUEST-OS-4.74_202001-01  |  24 stycznia 2020 r.  |  Post 4.76  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 8 stycznia 2020 r. | 5 marca 2020 r. |  
|~~WA-GUEST-OS-4.72_201911-01~~| 5 grudnia 2019 r. | 24 stycznia 2020 r. |  
|~~WA-GUEST-OS-4.71_201910-01~~| 1 listopada 2019 r. | 8 stycznia 2020 r. |  
|~~WA-GUEST-OS-4.70_201909-01~~| 7 października 2019 r. | 5 grudnia 2019 r. |  
|~~WA-GUEST-OS-4.69_201908-01~~| 4 sierpnia 2019 r. | 1 listopada 2019 r. |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26 lipca 2019 r.  | 7 października 2019 r. |
|~~WA-GUEST-OS-4.67_201906-01~~| 8 lipca 2019 r. |4 sierpnia 2019 r. |
|~~WA-GUEST-OS-4.66_201905-01~~ |6 czerwca 2019 r. |26 lipca 2019 r. |
|~~WA-GUEST-OS-4.65_201904-01~~ |7 maja 2019 r. |8 lipca 2019 r. |
|~~WA-GUEST-OS-4.64_201903-01~~ |26 marca 2019 r. |6 czerwca 2019 r. |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-3-releases"></a>Rodzina 3 zwalnia
**Windows Server 2012**

Zainstalowano program .NET Framework: 3.5, 4.5

| Ciąg konfiguracji | Data wydania | Wyłącz datę |
| --- | --- | --- |
|  WA-GUEST-OS-3.82_202002-01  |  5 marca 2020 r.  |  Post 3.84  |  
|  WA-GUEST-OS-3.81_202001-01  |  24 stycznia 2020 r.  |  Post 3.83  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 8 stycznia 2020 r. | 5 marca 2020 r. |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5 grudnia 2019 r. | 24 stycznia 2020 r. |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1 listopada 2019 r. | 8 stycznia 2020 r. |  
|~~WA-GUEST-OS-3.77_201909-01~~| 7 października 2019 r. | 5 grudnia 2019 r. |  
|~~WA-GUEST-OS-3.76_201908-01~~|  4 sierpnia 2019 r.  |  1 listopada 2019 r.  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26 lipca 2019 r. | 7 października 2019 r. |
|~~WA-GUEST-OS-3.74_201906-01~~| 8 lipca 2019 r. |4 sierpnia 2019 r. |
|~~WA-GUEST-OS-3.73_201905-01~~ |6 czerwca 2019 r. |26 lipca 2019 r. |
|~~WA-GUEST-OS-3.72_201904-01~~ |7 maja 2019 r. |8 lipca 2019 r. |
|~~WA-GUEST-OS-3.71_201903-01~~ |26 marca 2019 r. |6 czerwca 2019 r. |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-2-releases"></a>Rodzina 2 zwalnia
**Windows Server 2008 R2 SP1**

Zainstalowany program .NET Framework: 3,5 (w tym 2.0 i 3.0), 4.5

| Ciąg konfiguracji | Data wydania | Wyłącz datę |
| --- | --- | --- |
|  WA-GUEST-OS-2.95_202002-01  |  5 marca 2020 r.  |  Post 2.97  |  
|  WA-GUEST-OS-2.94_202001-01  |  24 stycznia 2020 r.  |  Post 2.96  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 8 stycznia 2020 r. | 5 marca 2020 r. |  
|~~WA-GUEST-OS-2.92_201911-01~~| 5 grudnia 2019 r. | 24 stycznia 2020 r. |  
|~~WA-GUEST-OS-2.91_201910-01~~| 1 listopada 2019 r. | 8 stycznia 2020 r. |  
|~~WA-GUEST-OS-2.90_201909-01~~| 7 października 2019 r. | 5 grudnia 2019 r. |  
|~~WA-GUEST-OS-2.89_201908-01~~| 4 sierpnia 2019 r. | 1 listopada 2019 r. |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26 lipca 2019 r. | 7 października 2019 r. |
|~~WA-GUEST-OS-2.87_201906-01~~|8 lipca 2019 r. | 4 sierpnia 2019 r. |
|~~WA-GUEST-OS-2.86_201905-01~~ |6 czerwca 2019 r. |26 lipca 2019 r. |
|~~WA-GUEST-OS-2.85_201904-01~~ |7 maja 2019 r. |8 lipca 2019 r. |
|~~WA-GUEST-OS-2.84_201903-01~~ |26 marca 2019 r. |6 czerwca 2019 r. |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="msrc-patch-updates"></a>Aktualizacje poprawek MSRC
Lista poprawek dołączonych do każdej miesięcznej wersji systemu operacyjnego gościa jest dostępna [tutaj.][patches]

## <a name="sdk-support"></a>Obsługa zestawu SDK
Mimo że [zasady wycofania dla narzędzia Azure SDK][retire policy sdk] wskazuje, że tylko wersje powyżej 2.2 są obsługiwane, określone rodziny systemu operacyjnego gościa umożliwiają korzystanie z wcześniejszych wersji. Należy zawsze używać najnowszych obsługiwanych SDK.

| Rodzina systemu operacyjnego gościa | Kompatybilne wersje SDK |
| --- | --- |
| 6 |Wersja 2.9.6+ |
| 5 |Wersja 2.9.5.1+ |
| 4 |Wersja 2.1+ |
| 3 |Wersja 1.8+ |
| 2 |Wersja 1.3+ |
| 1 |Wersja 1.0+ |

## <a name="guest-os-release-information"></a>Informacje o wydaniu systemu operacyjnego gościa
Istnieją trzy daty, które są ważne dla wersji systemu operacyjnego gościa: data **wydania,** data **wyłączenia** i data **wygaśnięcia.** System operacyjny gościa jest uważany za dostępny, gdy znajduje się w portalu i może być wybrany jako docelowy system operacyjny gościa. Gdy system operacyjny gościa osiągnie **datę wyłączeń,** jest usuwany z platformy Azure. Jednak wszystkie usługi w chmurze kierowane, że system operacyjny gościa będzie nadal działać normalnie.

Okno między **daty wyłączonej** i daty **wygaśnięcia** zapewnia bufor, aby łatwo przejść z jednego systemu operacyjnego gościa do jednego nowszego. Jeśli używasz *automatycznego* systemu operacyjnego gościa, zawsze będziesz w najnowszej wersji i nie musisz się martwić, że wygaśnie.

Po upływie daty **wygaśnięcia,** każda usługa w chmurze nadal przy użyciu tego systemu operacyjnego gościa zostanie zatrzymana, usunięta lub zmuszona do uaktualnienia. Możesz przeczytać więcej o polityce emerytalnej [tutaj][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Wyjaśnienie wersji rodzinnej systemu operacyjnego gościa
Rodziny systemów operacyjnych gościa są oparte na wydanych wersjach systemu Microsoft Windows Server. System operacyjny gościa jest podstawowym systemem operacyjnym, na który działają usługi Azure Cloud Services. Każdy system operacyjny gościa ma rodzinę, wersję i numer wydania.

* **Rodzina systemu operacyjnego gościa**  
  Wersja systemu operacyjnego Windows Server, na podstawie których znajduje się system operacyjny gościa. Na przykład *rodzina 3* jest oparta na systemie Windows Server 2012.
* **Wersja systemu operacyjnego gościa**  
  Specyficzne dla obrazu rodziny systemu operacyjnego gościa oraz odpowiednich poprawek [Centrum odpowiedzi zabezpieczeń firmy Microsoft (MSRC),][msrc] które są dostępne w dniu wyprodukowanym nowej wersji systemu operacyjnego gościa. Nie wszystkie poprawki mogą być dołączone.

    Liczby zaczynają się od 0 i przyrost o 1 za każdym razem, gdy zostanie dodany nowy zestaw aktualizacji. Końcowe zera są wyświetlane tylko wtedy, gdy są ważne. Oznacza to, że wersja 2.10 jest inną, znacznie nowszą wersją niż wersja 2.1.
* **Wydanie systemu operacyjnego gościa**  
  Ponowne wydanie wersji systemu operacyjnego gościa. Ponowne wydanie występuje, jeśli firma Microsoft wyszukuje problemy podczas testowania; wymagających zmian. Najnowsza wersja zawsze zastępuje wszelkie poprzednie wersje, publiczne lub nie. Witryna Azure portal zezwala użytkownikom tylko na wybranie najnowszej wersji dla danej wersji. Wdrożenia uruchomione w poprzedniej wersji zwykle nie są wymuszane uaktualnianie w zależności od ważności błędu.

W poniższym przykładzie 2 to rodzina, 12 to wersja, a "rel2" jest wydaniem.

**Wydanie systemu operacyjnego gościa** - 2.12 rel2

**Ciąg konfiguracji dla tej wersji** — WA-GUEST-OS-2.12_201208-02

Ciąg konfiguracji dla systemu operacyjnego gościa ma te same informacje osadzone w nim, wraz z datą pokazującą, które poprawki MSRC zostały uwzględnione dla tej wersji. W tym przykładzie poprawki MSRC utworzone dla systemu Windows Server 2008 R2 do sierpnia 2012 r. włącznie zostały uwzględnione. Uwzględniono tylko poprawki mające zastosowanie do tej wersji systemu Windows Server. Na przykład jeśli poprawka MSRC dotyczy pakietu Microsoft Office, nie zostanie uwzględniona, ponieważ ten produkt nie jest częścią obrazu podstawowego systemu Windows Server.

## <a name="guest-os-system-update-process"></a>Proces aktualizacji systemu operacyjnego gościa
Ta strona zawiera informacje o nadchodzących wydaniach systemu operacyjnego gościa. Klienci wskazali, że chcą wiedzieć, kiedy nastąpi wydanie, ponieważ ich role usługi w chmurze uruchomią się ponownie, jeśli są ustawione na aktualizację "Automatyczna". Wydania systemu operacyjnego gościa zazwyczaj występują 2-3 tygodnie po wydaniu aktualizacji MSRC, która występuje w drugi wtorek każdego miesiąca. Nowe wersje zawierają wszystkie odpowiednie poprawki MSRC dla każdej rodziny systemu operacyjnego gościa.

Platforma Microsoft Azure stale wydaje aktualizacje. System operacyjny gościa jest tylko jedną taką aktualizację w potoku. Na wydanie może mieć wpływ wiele czynników zbyt licznych, aby wymienić tutaj. Ponadto platforma Azure działa na dosłownie setkach tysięcy maszyn. Oznacza to, że nie można podać dokładnej daty i godziny, kiedy twoja rola zostanie ponownie uruchomiona. Pracujemy nad planem ograniczenia lub ponownego uruchomienia czasu.

Po opublikowaniu nowej wersji systemu operacyjnego gościa może upłynąć trochę czasu, aby w pełni propagować na platformie Azure. Ponieważ usługi są aktualizowane do nowego systemu operacyjnego gościa, są one ponownie uruchamiane honorując domeny aktualizacji. Usługi ustawione na użycie aktualizacji "Automatyczne" otrzymają najpierw wydanie. Po aktualizacji zobaczysz nową wersję systemu operacyjnego gościa wymienioną dla usługi w witrynie Azure portal. W tym okresie mogą wystąpić ponowne wydanie. Niektóre wersje mogą być wdrażane przez dłuższy czas, a automatyczne ponowne uruchamianie uaktualnień może nie wystąpić przez wiele tygodni po oficjalnej dacie wydania. Gdy system operacyjny gościa jest dostępny, można następnie jawnie wybrać tę wersję z portalu lub w pliku konfiguracji.

Aby uzyskać wiele cennych informacji na temat ponownego uruchomienia i wskaźników, aby uzyskać więcej informacji szczegółów technicznych aktualizacji systemu operacyjnego gościa i hosta, zobacz wpis w blogu MSDN zatytułowany [Ponowne uruchamianie wystąpienia roli z powodu uaktualnień systemu operacyjnego][restarts].

Jeśli ręcznie zaktualizujesz system operacyjny gościa, zobacz [zasady wycofywania systemu operacyjnego gościa, aby][retirepolicy] uzyskać dodatkowe informacje.

## <a name="guest-os-supportability-and-retirement-policy"></a>Obsługa systemu operacyjnego gościa i polityka emerytalna
Polityka obsługi systemu operacyjnego gościa i polityka emerytalna jest wyjaśniona [tutaj][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Kanał informacyjny RSS aktualizacji systemu operacyjnego gościa]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Więcej]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
