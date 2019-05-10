---
title: Dowiedz się więcej o najnowszej wersji systemu operacyjnego gościa platformy Azure | Dokumentacja firmy Microsoft
description: Najnowsze informacje o wersji i zgodność zestawu SDK dla systemu operacyjnego gościa usługi w chmurze platformy Azure.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/8/2019
ms.author: raiye
ms.openlocfilehash: 71f66129aeec4f4c8b470a421caade22960d2d64
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473213"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Wersje systemów operacyjnych gościa platformy Azure i zgodności zestawów SDK
Zapewnia zwalnia z aktualne informacje o najnowszych systemu operacyjnego gościa platformy Azure dla usług w chmurze. Te informacje pomagają planowanie ścieżki uaktualnienia przed wyłączeniem systemie operacyjnym gościa. Jeśli konfigurujesz role do użycia *automatyczne* aktualizacji systemu operacyjnego gościa, zgodnie z opisem w [ustawienia aktualizacji systemu operacyjnego gościa Azure][Azure Guest OS Update Settings], nie jest istotne, przeczytanie na tej stronie.

> [!IMPORTANT]
> Ta strona dotyczy usługi w chmurze ról sieć web i procesów roboczych uruchamianych na podstawie systemu operacyjnego gościa. Robi **nie stosuje się** do maszyn wirtualnych IaaS.
>
>


> [!TIP]
>  Subskrybuj [Kanał informacyjny RSS aktualizacji systemu operacyjnego gościa] otrzymywać powiadomienia o wszystkich zmianach systemu operacyjnego gościa.
>
>

> [!IMPORTANT]
> Tylko najnowsze 2 wersje systemu operacyjnego gościa będzie obsługiwana i dostępna w witrynie Azure portal.
>
>

Nie wiesz o tym, jak zaktualizować system operacyjny gościa? Sprawdź [to] [ cloud updates] się.

## <a name="news-updates"></a>Aktualizacje wiadomości

###### <a name="may-7-2019"></a>**7 maja 2019 r.**
Kwietnia system operacyjny gościa został wydany.

###### <a name="march-26-2019"></a>**26 marca 2019 r.**
System operacyjny gościa marca został wydany.

###### <a name="march-12-2019"></a>**12 marca 2019 r.**
System operacyjny gościa lutego został wydany.

###### <a name="february-5-2019"></a>**5 lutego 2019 r.**
System operacyjny gościa stycznia został wydany.

###### <a name="january-24-2019"></a>**24 stycznia 2019 r.**
Została wydana 6 z rodziny systemów operacyjnych gościa (2019 systemu Windows Server).

###### <a name="january-7-2019"></a>**7 stycznia 2019 r.**
System operacyjny gościa grudnia został wydany.

###### <a name="december-14-2018"></a>**14 grudnia 2018 r.**
System operacyjny gościa listopada został wydany.

###### <a name="november-8-2018"></a>**8 listopada 2018 r.**
System operacyjny gościa października został wydany.

###### <a name="october-12-2018"></a>**12 października 2018 r.**
System operacyjny gościa września został wydany.

## <a name="releases"></a>Wersje

## <a name="family-6-releases"></a>Zwalnia rodziny 6
**Windows Server 2019**

Zainstalowane środowisko .NET framework: 3.5, 4.7.2

> [!NOTE]
> Zestaw Windows Azure SDK dla platformy .NET — 3.0 można pobrać [tutaj][Windows Azure SDK].
>
>Kroki instalacji:
>1. Odinstaluj wszystkie starsze wersje MicrosoftAzureAuthoringTools*.msi
>2. Zainstaluj [zestaw Azure SDK dla platformy .NET — 3.0][Windows Azure SDK]
>3. Uruchom ponownie komputer
>4. Utwórz nowy projekt usługi w chmurze i Dodaj jedną rolę procesu roboczego
>5. Zmień rodziny systemów operacyjnych na 6 i utworzyć pakiet
>6. Wdrażanie pakietu na platformie Azure przy użyciu witryny Azure portal lub programu Visual Studio
>


| Ciąg konfiguracji | Data wydania | Wyłącz daty |
| --- | --- | --- |
| WA-GUEST-OS-6.6_201904-01 |7 maja 2019 r. |Wpis 6.8 |
| WA-GUEST-OS-6.5_201903-01 |26 marca 2019 r. |Wpis 6.7 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-6.3_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 stycznia 2019 r. |5 lutego 2019 r. |

## <a name="family-5-releases"></a>Zwalnia Family 5
**Windows Server 2016**

Zainstalowane środowisko .NET framework: 3.5, 4.6.2

> [!NOTE]
> Hasła protokołu RDP dla rodziny systemów operacyjnych 5 musi być co najmniej 10 znaków.
>


| Ciąg konfiguracji | Data wydania | Wyłącz daty |
| --- | --- | --- |
| WA-GUEST-OS-5.30_201904-01 |7 maja 2019 r. |Wpis 5.32 |
| WA-GUEST-OS-5.29_201903-01 |26 marca 2019 r. |Wpis 5.31 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-5.27_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-5.25_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-4-releases"></a>Zwalnia rodziny w wersji 4
**Windows Server 2012 R2**

Zainstalowane środowisko .NET framework: 3.5, 4.5.1

| Ciąg konfiguracji | Data wydania | Wyłącz daty |
| --- | --- | --- |
| WA-GUEST-OS-4.65_201904-01 |7 maja 2019 r. |Wpis 4.67 |
| WA-GUEST-OS-4.64_201903-01 |26 marca 2019 r. |Wpis 4.66 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-4.62_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-4.60_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-3-releases"></a>Zwalnia rodziny 3
**Windows Server 2012**

Zainstalowane środowisko .NET framework: 3.5, 4.5

| Ciąg konfiguracji | Data wydania | Wyłącz daty |
| --- | --- | --- |
| WA-GUEST-OS-3.72_201904-01 |7 maja 2019 r. |Wpis 3.74 |
| WA-GUEST-OS-3.71_201903-01 |26 marca 2019 r. |Wpis 3,73 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-3.69_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-3.67_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="family-2-releases"></a>Zwalnia rodziny 2
**Windows Server 2008 R2 SP1**

Zainstalowane środowisko .NET framework: 3.5 (w tym w wersji 2.0 i 3.0)

| Ciąg konfiguracji | Data wydania | Wyłącz daty |
| --- | --- | --- |
| WA-GUEST-OS-2.85_201904-01 |7 maja 2019 r. |Wpis 2,87 |
| WA-GUEST-OS-2.84_201903-01 |26 marca 2019 r. |Wpis 2.86 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12 marca 2019 r. |7 maja 2019 r. |
|~~WA-GUEST-OS-2.82_201901-01~~ |5 lutego 2019 r. |26 marca 2019 r. |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 stycznia 2019 r. |12 marca 2019 r. |
|~~WA-GUEST-OS-2.80_201811-01~~ |14 grudnia 2018 r. |5 lutego 2019 r. |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 listopada 2018 r. |7 stycznia 2019 r. |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 października 2018 r. |14 grudnia 2018 r. |

## <a name="msrc-patch-updates"></a>Centrum MSRC poprawek i aktualizacji
Lista poprawek, które są dołączone do wydania systemu operacyjnego gościa w każdym miesiącu jest dostępna [tutaj][patches].

## <a name="sdk-support"></a>Obsługa zestawu SDK
Mimo że [zasady wycofywania z zestawu Azure SDK] [ retire policy sdk] oznacza, że tylko wersje powyżej 2.2 są obsługiwane, określonej rodziny systemów operacyjnych gościa pozwalać pozwala korzystać z wcześniejszych wersji. Należy zawsze używać najnowszej obsługiwanym zestawem SDK.

| Rodzina systemów operacyjnych gościa | Zgodne wersje zestawu SDK |
| --- | --- |
| 6 |Wersja 2.9.6+ |
| 5 |Wersja 2.9.5.1+ |
| 4 |W wersji 2.1 + |
| 3 |W wersji 1.8 + |
| 2 |W wersji 1.3 + |
| 1 |W wersji 1.0 + |

## <a name="guest-os-release-information"></a>Informacje o wersji systemu operacyjnego gościa
Istnieją trzy daty, które są istotne dla wersji systemu operacyjnego gościa: **wersji** daty, **wyłączone** datę i **wygaśnięcia** daty. System operacyjny gościa jest uważana za dostępną w portalu i można wybrać jako docelowy system operacyjny gościa. Gdy osiągnie systemie operacyjnym gościa **wyłączone** daty, zostanie ono usunięte z systemu Azure. Jednak dowolnej usługi w chmurze przeznaczonych dla tego systemu operacyjnego gościa nadal będzie działać w zwykły sposób.

Okno między **wyłączone** daty i **wygaśnięcia** Data zawiera bufor do łatwo przejścia z jednego systemu operacyjnego gościa do jednego, które są nowsze. Jeśli używasz *automatyczne* jako system operacyjny gościa, zawsze będzie w najnowszej wersji i nie trzeba już martwić się o jego wygaśnięcia.

Gdy **wygaśnięcia** data przekazuje dowolnej usługi w chmurze nadal przy użyciu tego systemu operacyjnego gościa zostaną zatrzymane, usunięte bądź zmuszeni do uaktualnienia. Możesz dowiedzieć się więcej o zasadach wycofanie [tutaj][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Wyjaśnienie wersji rodzin systemów operacyjnych gościa
Rodzin systemów operacyjnych gościa są oparte na wydane wersje systemu Microsoft Windows Server. System operacyjny gościa jest uruchamiana w usługach Azure Cloud Services na system operacyjny. Każdy system operacyjny gościa ma rodziny, wersji i wydania numer.

* **Rodzina systemów operacyjnych gościa**  
  Wersja systemu operacyjnego Windows Server, opartego na systemie operacyjnym gościa. Na przykład *rodziny 3* jest oparty na systemie Windows Server 2012.
* **Wersja systemu operacyjnego gościa**  
  Specyficzne dla rodziny obraz systemu operacyjnego gościa oraz odpowiednie [Microsoft Security Response Center (MSRC)] [ msrc] poprawek, które są dostępne na datę produkowaną jest nowa wersja systemu operacyjnego gościa. Nie wszystkie poprawki mogą zostać uwzględnione.

    Numery rozpoczynają się od 0 i Zwiększaj go o 1 w każdym razem, gdy zostanie dodany nowy zestaw aktualizacji. Zera końcowe są wyświetlane tylko jeśli jest to ważne. W wersji 2.10 jest nieco inny, znacznie nowszej niż wersja 2.1.
* **Wersja systemu operacyjnego gościa**  
  Ponowne wydanie wersji systemu operacyjnego gościa. Ponowne wydanie występuje wtedy, gdy Microsoft znalezione problemy podczas badania; konieczności wprowadzania zmian. Najnowsza wersja zawsze zastępuje wszystkie poprzednie wersje, publicznego lub nie. Witryna Azure portal będzie tylko zezwalać użytkownikom na wybranie najnowszej wersji dla danej wersji. Wdrożenia z systemem wcześniejszej wersji nie są zwykle życie uaktualnienia w zależności od ważności usterki.

W poniższym przykładzie 2 to rodzina, 12, jest to wersja i "rel2" jest to wersja.

**Wersji systemu operacyjnego gościa** — 2.12 rel2

**Ciąg konfiguracji dla tej wersji** -WA-GOŚCIA — system operacyjny — 2.12_201208-02

Ciąg konfiguracji dla systemu operacyjnego gościa ma to te same informacje osadzone w nim, oraz Data pokazywanie poprawek MSRC, które zostały rozważone w tej wersji. W tym przykładzie MSRC poprawki wygenerowane dla systemu Windows Server 2008 R2 do i łącznie — sierpień 2012 zostały uwzględnione w. Uwzględniane są tylko poprawki specjalnie mające zastosowanie do tej wersji systemu Windows Server. Na przykład jeśli poprawka MSRC odnosi się do programu Microsoft Office, nie będzie uwzględniony, ponieważ ten produkt nie jest częścią obrazu podstawowego systemu Windows Server.

## <a name="guest-os-system-update-process"></a>Proces aktualizacji systemu systemu operacyjnego gościa
Ta strona zawiera informacje na temat przyszłych wersji systemu operacyjnego gościa. Klienci wskazali, że chce wiedzieć, gdy wystąpi wydania, ponieważ ich ról usługi w chmurze zostanie ponownie uruchomiony, jeśli są one ustawione na "Automatyczny" Aktualizacja. Wersje systemu operacyjnego gościa występują zwykle 2 – 3 tygodnie po MSRC aktualizacji wersji, która występuje w drugi wtorek każdego miesiąca. Nowe wersje zawierają wszystkie odpowiednie poprawki MSRC dla każdej rodziny systemów operacyjnych gościa.

Microsoft Azure stale udostępnia aktualizacje. System operacyjny gościa jest tylko jeden taki aktualizacji w potoku. Wydanie mogą mieć wpływ wiele czynników za duża, aby wyświetlić listę w tym miejscu. Ponadto platforma Azure działa w dosłownie setki tysięcy maszyn. Oznacza to, że jest wręcz niemożliwe dokładnej daty i godziny, kiedy nastąpi ponowne uruchomienie komputera usługi ról. Pracujemy nad planem do ograniczenia lub czas ponownego uruchamiania.

Po opublikowaniu nowej wersji systemu operacyjnego gościa, może upłynąć czas pełni propagacji na platformie Azure. Ponieważ usługi są aktualizowane do nowego systemu operacyjnego gościa, ich wykonywany jest ponowny rozruch zapewniane domenach aktualizacji. Usługi skonfigurowane do korzystania z aktualizacji "Automatycznie", otrzyma pierwszego wydania. Po aktualizacji zostanie wyświetlony nowej wersji systemu operacyjnego gościa, na liście usługi w witrynie Azure portal. Ponownych wydań mogą wystąpić w trakcie tego okresu. Niektóre wersje mogą wdrożyć przez dłuższy czas i automatycznego ponownego uruchamiania uaktualniania nie może wystąpić wiele tygodni po dacie oficjalnym wydaniem. Po udostępnieniu systemu operacyjnego gościa, następnie jawnie można tę wersję z portalu lub w pliku konfiguracji.

Na dużym stopniem cennych informacji na temat ponownego uruchamiania i wskaźniki, aby uzyskać więcej szczegółów technicznych informacji o aktualizacji dla gościa i systemu operacyjnego hosta, zobacz zatytułowany post na blogu MSDN [roli wystąpienie powoduje ponowne uruchomienie z powodu uaktualnienia systemu operacyjnego] [ restarts].

Jeśli ręcznie zaktualizować system operacyjny gościa, zobacz [zasady wycofywania systemu operacyjnego gościa] [ retirepolicy] Aby uzyskać dodatkowe informacje.

## <a name="guest-os-supportability-and-retirement-policy"></a>Zasady wycofywania i możliwości obsługi systemu operacyjnego gościa
Zasady wycofywania i możliwości obsługi systemu operacyjnego gościa zostało wyjaśnione [tutaj][retirepolicy].

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
