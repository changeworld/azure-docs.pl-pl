---
title: Dowiedz się więcej o najnowszych wersjach systemu operacyjnego gościa Azure | Dokumentacja firmy Microsoft
description: Najnowsze informacje o wersji i zestawu SDK zgodności w systemie operacyjnym gościa usługi w chmurze Azure.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 6/1/2018
ms.author: raiye
ms.openlocfilehash: dd6a9b3e8777c6efa4d6db07d4d65c86463f6cfe
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063616"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure wersji systemu operacyjnego gościa i zgodność pakietu SDK
Zapewnia użytkownikowi aktualne informacje o najnowszych systemu operacyjnego gościa Azure wersje usługi w chmurze. Informacje te pomagają sobie planowanie uaktualnienia przed wyłączeniem systemu operacyjnego gościa. Jeśli konfigurujesz role do użycia *automatyczne* aktualizacji systemu operacyjnego gościa, zgodnie z opisem w [ustawienia aktualizacji systemu operacyjnego gościa Azure][Azure Guest OS Update Settings], nie jest istotne, aby odczytać tę stronę.

> [!IMPORTANT]
> Ta strona dotyczy ról usługi w chmurze sieci web i proces roboczy, które działają ponad systemu operacyjnego gościa. Robi **nie stosuje się** do maszyn wirtualnych IaaS.
>
>


> [!TIP]
>  Subskrybuj [Źródło danych RSS aktualizacji systemu operacyjnego gościa] na najbardziej odpowiednim powiadomienie na wszystkie zmiany systemu operacyjnego gościa.
>
>

> [!IMPORTANT]
> Począwszy od listopada wdrożenia, tylko najnowsze 2 wersje systemu operacyjnego gościa będą obsługiwane i dostępne w portalu Azure.
>
>

Nie wiesz o tym, jak zaktualizować system operacyjny gościa? Sprawdź [to] [ cloud updates] wychodzących.

## <a name="news-updates"></a>Wiadomości
###### <a name="june-1-2018"></a>**1 czerwca 2018**
Wydała może systemu operacyjnego gościa.

###### <a name="may-4-2018"></a>**4 maja 2018**
Wydała kwietnia systemu operacyjnego gościa.

###### <a name="april-6-2018"></a>**6 kwietnia 2018**
Wydała marca systemu operacyjnego gościa.

###### <a name="march-19-2018"></a>**19 marca 2018**
Wydała lutego systemu operacyjnego gościa.

###### <a name="january-29-2018"></a>**29 stycznia 2018**
Systemu operacyjnego gościa stycznia została wydana 2 rodzin systemu operacyjnego (WA-GOŚCIA — systemu operacyjnego — 2.70_201801-01) & 3 (WA-GOŚCIA-systemu operacyjnego — 3.57_201801-01)

###### <a name="january-4-2018"></a>**4 stycznia 2018**
Systemu operacyjnego gościa stycznia została wydana 4 rodzin systemu operacyjnego (WA-GOŚCIA — systemu operacyjnego — 4.50_201801-01) & 5 (WA-GOŚCIA-systemu operacyjnego — 5.15_201801-01) i zawiera poprawki zabezpieczeń ważne.  

###### <a name="january-4-2018"></a>**4 stycznia 2018**
Wydała grudnia systemu operacyjnego gościa.

###### <a name="december-14-2017"></a>**14 grudnia 2017 r.**
Wydała listopada systemu operacyjnego gościa.

###### <a name="november-8-2017"></a>**8 listopada 2017 r.**
Wydała października systemu operacyjnego gościa.

###### <a name="october-6-2017"></a>**6 października 2017 r.**
Wydała września systemu operacyjnego gościa. W wersji Windows Server 2016 września netfx3 jest domyślnie włączona. Należy dodać odbiorców "narzędzia dism / online/Disable-Feature /featurename:netfx3" w ich OnStart, jeśli ich przepływu pracy wymagane do uruchomienia aplikacji .NET 2.x o środowisku uruchomieniowym 4.x lub jeśli wykonywania aplikacji 2.x .NET obsługiwany błąd i został uruchomiony 4.x aplikacji .NET.



## <a name="releases"></a>zwalnia
## <a name="family-5-releases"></a>Zwalnia rodziny 5
**Windows Server 2016**

Zainstalowany program .NET framework: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Daty z * są może ulec zmianie.
>
> Hasło RDP 5 rodziny systemu operacyjnego musi mieć co najmniej 10 znaków.
>

| Ciąg konfiguracji | Data wydania | Wyłącz daty | Wygasłe daty |
| --- | --- | --- | --- |
| WA-GOŚCIA-OS-5.19_201805-01 |1 czerwca 2018 |Post 5.21 |TBD |
| WA-GOŚCIA-OS-5.18_201804-01 |4 maja 2018 |Post 5.20 |TBD |
|~~WA-GOŚCIA-OS-5.17_201803-01~~ |6 kwietnia 2018 |1 czerwca 2018|TBD |
|~~WA-GOŚCIA-OS-5.16_201802-01~~ |12 marca 2018 |4 maja 2018 |TBD |
|~~WA-GUEST-OS-5.15_201801-01~~ |4 stycznia 2018 |6 kwietnia 2018 |TBD |
|~~WA-GUEST-OS-5.14_201712-01~~ |4 stycznia 2018 |12 marca 2018 |TBD |
|~~WA-GUEST-OS-5.13_201711-01~~ |14 grudnia 2017 r. |4 stycznia 2018|TBD |
|~~WA-GUEST-OS-5.12_201710-02~~ |8 listopada 2017 r. |4 stycznia 2018 |TBD |
|~~WA-GUEST-OS-5.11_201709-01~~ |6 października 2017 r. |14 grudnia 2017 r. |TBD |
|~~WA-GUEST-OS-5.10_201708-01~~ |24 sierpnia 2017 r. |14 grudnia 2017 r. |TBD |


## <a name="family-4-releases"></a>Zwalnia rodziny 4
**Windows Server 2012 R2**

Zainstalowany program .NET framework: 4.0 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Daty z * są może ulec zmianie
>
>

| Ciąg konfiguracji | Data wydania | Wyłącz daty | Wygasłe daty |
| --- | --- | --- | --- |
| WA-GOŚCIA-OS-4.54_201805-01 |1 czerwca 2018 |Post 4.56 |TBD |
| WA-GOŚCIA-OS-4.53_201804-01 |4 maja 2018 |Post 4.55 |TBD |
|~~WA-GOŚCIA-OS-4.52_201803-01~~ |6 kwietnia 2018 |1 czerwca 2018 |TBD |
|~~WA-GOŚCIA-OS-4.51_201802-01~~ |12 marca 2018 |4 maja 2018 |TBD |
|~~WA-GUEST-OS-4.50_201801-01~~ |4 stycznia 2018 |6 kwietnia 2018 |TBD |
|~~WA-GUEST-OS-4.49_201712-01~~ |4 stycznia 2018 |12 marca 2018 |TBD |
|~~WA-GUEST-OS-4.48_201711-01~~ |14 grudnia 2017 r. |4 stycznia 2018 |TBD |
|~~WA-GUEST-OS-4.47_201710-02~~ |8 listopada 2017 r. |4 stycznia 2018 |TBD |
|~~WA-GUEST-OS-4.46_201709-01~~ |6 października 2017 r. |14 grudnia 2017 r. |TBD |
|~~WA-GUEST-OS-4.45_201708-01~~ |24 sierpnia 2017 r. |14 grudnia 2017 r. |TBD |



## <a name="family-3-releases"></a>Zwalnia rodziny 3
**Windows Server 2012**

Zainstalowany program .NET framework: 4.0 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Daty z * są może ulec zmianie
>
>

| Ciąg konfiguracji | Data wydania | Wyłącz daty | Wygasłe daty |
| --- | --- | --- | --- |
| WA-GOŚCIA-OS-3.61_201805-01 |1 czerwca 2018 |Post 3.63 |TBD |
| WA-GOŚCIA-OS-3.60_201804-01 |4 maja 2018 |Post 3.62 |TBD |
|~~WA-GOŚCIA-OS-3.59_201803-01~~ |6 kwietnia 2018 |1 czerwca 2018 |TBD |
|~~WA-GOŚCIA-OS-3.58_201802-01~~ |19 marca 2018 |4 maja 2018 |TBD |
|~~WA-GUEST-OS-3.57_201801-01~~ |29 stycznia 2018 |6 kwietnia 2018 |TBD |
|~~WA-GUEST-OS-3.56_201712-01~~ |4 stycznia 2018 |19 marca 2018 |TBD |
|~~WA-GUEST-OS-3.55_201711-01~~ |14 grudnia 2017 r. |29 stycznia 2018 |TBD |
|~~WA-GUEST-OS-3.54_201710-02~~ |8 listopada 2017 r. |4 stycznia 2018 |TBD |
|~~WA-GUEST-OS-3.53_201709-01~~ |6 października 2017 r. |14 grudnia 2017 r. |TBD |
|~~WA-GUEST-OS-3.52_201708-01~~ |24 sierpnia 2017 r. |14 grudnia 2017 r. |TBD |


## <a name="family-2-releases"></a>Zwalnia rodziny 2
**Windows Server 2008 R2 z dodatkiem SP1**

Zainstalowany program .NET framework: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Daty z * są może ulec zmianie
>
>

| Ciąg konfiguracji | Data wydania | Wyłącz daty | Wygasłe daty |
| --- | --- | --- | --- |
| WA-GOŚCIA-OS-2.74_201805-01 |1 czerwca 2018 |Post 2.76 |TBD |
| WA-GOŚCIA-OS-2.73_201804-01 |4 maja 2018 |Post 2,75 |TBD |
|~~WA-GOŚCIA-OS-2.72_201803-01~~ |6 kwietnia 2018 |1 czerwca 2018 |TBD |
|~~WA-GOŚCIA-OS-2.71_201802-01~~ |12 marca 2018 |4 maja 2018 |TBD |
|~~WA-GUEST-OS-2.70_201801-01~~ |29 stycznia 2018 |6 kwietnia 2018 |TBD |
|~~WA-GUEST-OS-2.69_201712-01~~ |4 stycznia 2018 |12 marca 2018 |TBD |
|~~WA-GUEST-OS-2.68_201711-01~~ |14 grudnia 2017 r. |29 stycznia 2018 |TBD |
|~~WA-GUEST-OS-2.67_201710-02~~ |8 listopada 2017 r. |4 stycznia 2018 |TBD |
|~~WA-GUEST-OS-2.66_201709-01~~ |6 października 2017 r. |14 grudnia 2017 r. |TBD |
|~~WA-GUEST-OS-2.65_201708-01~~ |24 sierpnia 2017 r. |14 grudnia 2017 r. |TBD |


## <a name="msrc-patch-updates"></a>MSRC poprawek i aktualizacji
Lista poprawek, które są dołączone do każdej miesięcznej wersji systemu operacyjnego gościa jest dostępna [tutaj][patches].

## <a name="sdk-support"></a>Obsługa w zestawie SDK
Mimo że [wycofywania zasad dla zestawu Azure SDK] [ retire policy sdk] wskazuje, czy tylko wersje powyżej 2.2 są obsługiwane, określonych rodzin systemu operacyjnego gościa umożliwiają używanie wcześniejszych wersji. Należy zawsze używać najnowszej obsługiwanym zestawem SDK.

| Rodzina systemów operacyjnych gościa | Wersje zgodne zestawu SDK |
| --- | --- |
| 5 |Wersja 2.9.5.1+ |
| 4 |W wersji 2.1 + |
| 3 |W wersji 1.8 + |
| 2 |W wersji 1.3 + |
| 1 |W wersji 1.0 + |

## <a name="guest-os-release-information"></a>Informacje o wersji systemu operacyjnego gościa
Istnieją trzy daty, które są ważne dla poszczególnych wersji systemu operacyjnego gościa: **wersji** daty, **wyłączone** daty, i **wygaśnięcia** daty. Systemu operacyjnego gościa jest uważany za dostępny, gdy znajduje się w portalu, a jako element docelowy systemu operacyjnego gościa można wybrać. Gdy osiągnie systemu operacyjnego gościa **wyłączone** daty, zostanie ono usunięte z platformy Azure. Jednak wszystkie usługi w chmurze przeznaczonych dla tego systemu operacyjnego gościa nadal będzie działać jako standardowa.

Okno między **wyłączone** daty i **wygaśnięcia** Data zawiera buforu do łatwe przejście z jednego systemu operacyjnego gościa do jednego nowsza. Jeśli używasz *automatyczne* jako system operacyjny gościa, zawsze będzie w najnowszej wersji i nie trzeba martwić się o jego wygaśnięcia.

Gdy **wygaśnięcia** data przekazuje wszystkie usługi w chmurze w dalszym ciągu używają tego systemu operacyjnego gościa będzie można zatrzymać, usunięty lub zmuszony do uaktualnienia. Więcej o zasadach wycofania [tutaj][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Wyjaśnienie rodziny wersja systemu operacyjnego gościa
Rodzin systemu operacyjnego gościa są oparte na wersji systemu Microsoft Windows Server. System operacyjny gościa jest system operacyjny, działającą z usługi w chmurze Azure. Każdego systemu operacyjnego gościa ma rodziny, wersji i wydania numer.

* **Rodzina systemów operacyjnych gościa**  
  Wersja systemu operacyjnego Windows Server, na podstawie systemu operacyjnego gościa. Na przykład *rodziny 3* jest oparta na systemie Windows Server 2012.
* **Wersja systemu operacyjnego gościa**  
  Specyficzne dla rodziny obraz systemu operacyjnego gościa oraz odpowiednie [Microsoft Security odpowiedzi Center (MSRC)] [ msrc] poprawki, które są dostępne w dniu powstaje nowej wersji systemu operacyjnego gościa. Nie wszystkie poprawki można uwzględnić.

    Numery rozpoczynają się od 0 i 1 zwiększenia zawsze, gdy zostanie dodany nowy zestaw aktualizacji. Końcowe zero są wyświetlane tylko jeśli jest to ważne. Wersja 2.10 jest inny, kiedy wersję niż wersja 2.1.
* **Wersja systemu operacyjnego gościa**  
  Ponowne wydanie wersji systemu operacyjnego gościa. Ponowne wydanie występuje, gdy zauważamy problemów podczas testowania; wymagające zmiany. Najnowsza wersja zawsze zastępuje wszystkie poprzednie wersje, publiczny lub nie. Azure portal tylko pozwoli użytkownikom do pobrania najnowszej wersji dla danej wersji. Wdrożenia z poprzedniej wersji nie są zwykle życie uaktualnienia w zależności od ważności usterki.

W poniższym przykładzie 2 to rodzina, 12, jest to wersja i "rel2" jest wersją.

**Wersji systemu operacyjnego gościa** — 2.12 rel2

**Ciągu konfiguracji dla tej wersji** — WA-GOŚCIA-OS-2.12_201208-02

Ciąg konfiguracji dla systemu operacyjnego gościa ma te same informacje osadzone w nim, oraz datę przedstawiający poprawki MSRC, które zostały uznane za w tej wersji. W tym przykładzie MSRC poprawek dla systemu Windows Server 2008 R2 do klasy i, w tym — sierpień 2012 zostały uwzględnione w. Uwzględniane są tylko poprawek, w szczególności stosowania do tej wersji systemu Windows Server. Na przykład jeśli poprawka MSRC odnosi się do programu Microsoft Office, nie zostanie on uwzględniony ponieważ tego produktu nie jest częścią obrazu podstawowego systemu Windows Server.

## <a name="guest-os-system-update-process"></a>Proces aktualizacji systemu systemu operacyjnego gościa
Ta strona zawiera informacje o kolejnych wersjach systemu operacyjnego gościa. Klienci wskazał, czy chce wiedzieć, kiedy zlecenia występuje, ponieważ ich role usługi w chmurze zostanie ponownie uruchomiony, jeśli są ustawione na "Automatyczny" aktualizacji. Wersje systemu operacyjnego gościa zwykle występuje co najmniej pięć (5) dni po MSRC aktualizacji wersji, która występuje w drugi wtorek każdego miesiąca. Nowości obejmują wszystkie odpowiednie poprawki MSRC dla każdej rodziny systemów operacyjnych gościa.

Microsoft Azure stale udostępnia aktualizacji. System operacyjny gościa jest tylko jeden taki aktualizacji w potoku. Zlecenia może niekorzystnie wpływać wiele czynników zbyt duża, aby wyświetlić w tym miejscu. Ponadto Azure wykorzystuje dosłownie setki tysięcy komputerów. Oznacza to, czy jest możliwe umieszczenie dokładnej dacie i godzinie, gdy zostanie ponownie uruchomiony z rolami. Pracujemy nad planu w celu ograniczenia lub czas ponownego uruchomienia.

Po opublikowaniu nowej wersji systemu operacyjnego gościa może upłynąć czas pełni są propagowane na platformie Azure. Ponieważ usługi są aktualizowane do nowego systemu operacyjnego gościa, ich są ponownie uruchamiane ramach domen aktualizacji. Usługi skonfigurowane do korzystania z aktualizacji "Automatyczny" otrzyma zlecenia pierwszy. Po aktualizacji zostanie wyświetlone na liście usługi w portalu Azure nowej wersji systemu operacyjnego gościa. Ponownych wydań mogą wystąpić podczas tego okresu. Niektóre wersje może wdrożyć przez dłuższy czas i automatyczne ponowne uruchomienie uaktualnienia nie może wystąpić wiele tygodni po dacie oficjalnego wydania. Po udostępnieniu systemu operacyjnego gościa, można jawnie wybrać tej wersji w portalu lub w pliku konfiguracji.

Dla dużą cennych informacji na temat ponownego uruchomienia i wskaźniki, aby uzyskać więcej informacji technicznych szczegółów aktualizacji systemu operacyjnego hosta i gościa, zobacz blog MSDN post zatytułowany [roli wystąpienia uruchamia ponownie z powodu uaktualnienia systemu operacyjnego] [ restarts].

Jeśli ręcznie zaktualizować system operacyjny gościa, zobacz [systemu operacyjnego gościa wycofywania zasad] [ retirepolicy] Aby uzyskać dodatkowe informacje.

## <a name="guest-os-supportability-and-retirement-policy"></a>Możliwość obsługi systemu operacyjnego gościa i wycofywania zasad
Zasady obsługi i wycofania systemu operacyjnego gościa jest wyjaśniono [tutaj][retirepolicy].

[cloud updates]: https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-update-azure-service
[Źródło danych RSS aktualizacji systemu operacyjnego gościa]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/en-us/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
