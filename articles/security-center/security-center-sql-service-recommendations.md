---
title: Ochrona usługi Azure SQL i danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę danych i usługi Azure SQL i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58880784"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrona usługi Azure SQL i danych w usłudze Azure Security Center
Usługa Azure Security Center analizuje stan zabezpieczeń zasobów platformy Azure. Gdy usługa Security Center zidentyfikuje potencjalnych luk w zabezpieczeniach, tworzy zaleceń, które przeprowadzą Cię przez proces konfigurowania wymaganych kontrolek.  Zalecenia odnoszą się do typów zasobów platformy Azure: maszyny wirtualne (VM), networking, SQL i dane i aplikacje.


### <a name="monitor-data-security"></a>Monitorowanie bezpieczeństwa danych

Po kliknięciu pozycji **Bezpieczeństwo danych** w sekcji **Zapobieganie** zostanie otwarty blok **Zasoby danych** z zaleceniami dotyczącymi usług SQL i Storage. Przedstawia on także [zalecenia](security-center-sql-service-recommendations.md) dotyczące ogólnej kondycji bazy danych. Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).

![Zasoby danych](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

W obszarze **Zalecenia SQL** można kliknąć dowolne zalecenie i uzyskać więcej szczegółów na temat dalszych działań w celu rozwiązania problemu. Poniższy przykład pokazuje rozszerzenie zalecenia **Inspekcja bazy danych i wykrywanie zagrożeń w bazach danych SQL**.

![Szczegółowe informacje o zaleceniach SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Blok **Włączanie inspekcji i wykrywania zagrożeń dla baz danych SQL** zawiera następujące informacje:

* Lista baz danych SQL
* Serwer, na którym znajdują się bazy danych SQL
* Informacje o tym, czy to ustawienie jest dziedziczone z serwera lub czy jest unikatowe w tej bazie danych
* Bieżący stan
* Ważność problemu

Po kliknięciu bazy danych w celu zastosowania tego zalecenia zostanie otwarty blok **Inspekcja i wykrywanie zagrożeń**, jak pokazano na poniższym ekranie.

![Inspekcja i wykrywanie zagrożeń](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Aby włączyć funkcję inspekcji, zaznacz pole wyboru **WŁĄCZONE** poniżej opcji **Inspekcja**.

## <a name="data-and-storage-recommendations"></a>Zalecenia dotyczące danych i magazynu

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Konto magazynu|20|Wymaganie bezpiecznego transferu na konto magazynu|Bezpieczny transfer to opcja, która wymusza na koncie magazynu w celu umożliwienia akceptowania żądań tylko z bezpiecznego połączenia (HTTPS). Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługi oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|Redis|20|Włącz tylko bezpiecznych połączeń do pamięci podręcznej Azure dla usługi Redis|Włącz tylko połączenia za pośrednictwem protokołu SSL w celu pamięć podręczna systemu Azure dla usługi Redis. Użyj bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|SQL|15|Włączanie funkcji Transparent Data Encryption w bazach danych SQL|Włącz technologię Transparent Data Encryption, aby chronić dane magazynowane i spełniać wymagania dotyczące zgodności.|
|SQL|15|Włączanie inspekcji dla serwerów SQL|Włączanie inspekcji dla serwerów SQL platformy Azure. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych).|
|SQL|15|Włączanie inspekcji dla baz danych SQL|Włącz inspekcję dla baz danych Azure SQL. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych).|
|Usługa Data lake analytics|15|Włącz szyfrowanie danych magazynowanych usługa Data Lake Analytics|Włączanie technologii transparent data encryption chronić dane magazynowane na platformie usługi Data Lake Analytics. Szyfrowanie w spoczynku jest niewidoczna, co oznacza, że usługa Data Lake Analytics automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobraniem. Nie ma zmian wymagane w aplikacji i usług, które współdziałają z usługą Data Lake Analytics z powodu szyfrowania. Szyfrowanie w spoczynku minimalizuje ryzyko utraty danych z kradzieżą fizyczną i pomaga również spełniają wymagania zgodności z przepisami.|
|Usługi Data lake store|15|Włącz szyfrowanie danych magazynowanych na potrzeby Data Lake Store|Włączanie technologii transparent data encryption chronić dane magazynowane na platformie usługi Data Lake Store. Szyfrowanie w spoczynku jest niewidoczna, co oznacza, że Data Lake Store automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobraniem. Nie trzeba wprowadzać zmian w aplikacji i usług, współpracujące z usługą Data Lake Store do obsługi szyfrowania. Szyfrowanie w spoczynku minimalizuje ryzyko utraty danych z kradzieżą fizyczną i pomaga również spełniają wymagania zgodności z przepisami.|
|Usługa Data lake analytics|5|Włącz dzienniki diagnostyki w usłudze Data Lake Analytics|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Usługi Data lake store|5|Włącz dzienniki diagnostyki w usłudze Azure Data Lake Store|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|SQL|30|Korygowanie luk w zabezpieczeniach w bazach danych SQL|Ocena luk w zabezpieczeniach SQL skanuje bazę danych pod kątem luk w zabezpieczeniach i udostępnia wszystkich odchyleń od najlepszych rozwiązań, takich jak nieprawidłowe konfiguracje, nadmierne uprawnienia i niechronione poufnych danych. Rozpoznawanie znaleziono luki w zabezpieczeniach można znacznie zwiększyć swoje stature zabezpieczeń bazy danych.|
|SQL|20|Aprowizowanie administrator usługi Azure AD dla programu SQL server|Aprowizuj administrator usługi Azure AD dla programu SQL server, aby włączyć uwierzytelnianie usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.|
|Konto magazynu|15|Wyłącz nieograniczony dostęp do konta magazynu|Przeprowadź inspekcję dostępu do sieci bez ograniczeń w ustawieniach zapory konta magazynu. Zamiast tego należy skonfigurować zasady sieci, dzięki czemu tylko aplikacje z dozwolonych sieci mogą uzyskać dostępu do konta magazynu. Aby zezwolić na połączenia z określonym Internet lub na klientach lokalnych, można przyznać dostęp do ruchu z określonych sieciach wirtualnych platformy Azure lub zakresy publicznych adresów IP w Internecie.|
|Konto magazynu|1|Migrowanie kont magazynu do zasoby modelu usługi Azure Resource Manager|Użyj nowego 2 usługi Azure Resource Manager dla konta magazynu zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrażania usługi Resource Manager i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla klucze tajne, uwierzytelnianie na podstawie usługi AD systemu Azure i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami.|

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
