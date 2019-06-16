---
title: Ochrona usługi Azure SQL i danych w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę danych i usługi Azure SQL i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: monhaber
ms.openlocfilehash: bbba5f380fddb4fdec43a7414e59778135c4e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428299"
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
|Konto magazynu|20|Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.|Bezpieczny transfer to opcja, która wymusza na koncie magazynu w celu umożliwienia akceptowania żądań tylko z bezpiecznego połączenia (HTTPS). Korzystanie z protokołu HTTPS zapewnia uwierzytelnianie między serwerem i usługi oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|Redis|20|Powinien być włączony tylko bezpiecznych połączeń z usługą Redis Cache|Włącz tylko połączenia za pośrednictwem protokołu SSL w celu pamięć podręczna systemu Azure dla usługi Redis. Użyj bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
|SQL|15|Transparent Data Encryption w bazach danych SQL powinno być włączone.|Włączanie technologii transparent data encryption do ochrony danych magazynowanych i spełnić wymagania dotyczące zgodności.|
|SQL|15|Inspekcja programu SQL server, powinno być włączone|Włączanie inspekcji dla serwerów SQL platformy Azure. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych).|
|Usługa Data lake analytics|5|Dzienniki diagnostyczne w usłudze Data Lake Analytics powinno być włączone.|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Usługi Data lake store|5|Dzienniki diagnostyczne w usłudze Azure Data Lake Store powinno być włączone.|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|SQL|30|Powinny zostać skorygowane luk w zabezpieczeniach w bazach danych SQL|Ocena luk w zabezpieczeniach SQL skanuje bazę danych pod kątem luk w zabezpieczeniach i udostępnia wszystkich odchyleń od najlepszych rozwiązań, takich jak nieprawidłowe konfiguracje, nadmierne uprawnienia i niechronione poufnych danych. Rozpoznawanie znaleziono luki w zabezpieczeniach można znacznie zwiększyć swoje stature zabezpieczeń bazy danych.|
|SQL|20|Aprowizowanie administrator usługi Azure AD dla programu SQL server|Aprowizuj administrator usługi Azure AD dla programu SQL server, aby włączyć uwierzytelnianie usługi Azure AD. Uwierzytelnianie usługi Azure AD umożliwia zarządzanie uprawnieniami uproszczone i scentralizowane identity management użytkowników bazy danych i innych usług firmy Microsoft.|
|Konto magazynu|15|Dostęp do konta magazynu z zaporą i konfiguracji sieci wirtualnej powinny być ograniczone|Przeprowadź inspekcję dostępu do sieci bez ograniczeń w ustawieniach zapory konta magazynu. Zamiast tego należy skonfigurować zasady sieci, dzięki czemu tylko aplikacje z dozwolonych sieci mogą uzyskać dostępu do konta magazynu. Aby zezwolić na połączenia z określonym Internet lub na klientach lokalnych, można przyznać dostęp do ruchu z określonych sieciach wirtualnych platformy Azure lub zakresy publicznych adresów IP w Internecie.|
|Konto magazynu|1|Konta magazynu powinny być migrowane do nowych zasobów usługi Azure Resource Manager|Użyj nowego 2 usługi Azure Resource Manager dla konta magazynu zapewnienie wzmocnienia zabezpieczeń, takich jak: silniejsze kontroli dostępu (RBAC), lepiej inspekcji, wdrażania usługi Resource Manager i nadzoru, dostęp do zarządzanych tożsamości i dostępu do magazynu kluczy dla klucze tajne, uwierzytelnianie na podstawie usługi AD systemu Azure i obsługa tagów i grupy zasobów, aby ułatwić zarządzanie zabezpieczeniami.|

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej na temat zalecenia, które mają zastosowanie do innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)

Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
