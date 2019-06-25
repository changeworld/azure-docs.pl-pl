---
title: Ochrona usług platformy Azure dane i Magazyn w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: Adresy tego dokumentu, zalecenia w usłudze Azure Security Center, które ułatwiają ochronę danych i usługi Azure SQL i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: monhaber
ms.openlocfilehash: 2ac0e4ebaafb8b0c9c79e885cecbefc5a65c1823
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275331"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Ochrona usług platformy Azure dane i Magazyn w usłudze Azure Security Center
W tym temacie dowiesz się, jak wyświetlać i zalecenia dotyczące implementowania zabezpieczeń dla danych i zasobów magazynowania. Usługa Azure Security Center znaleźć te zalecenia, podczas analizowania stan zabezpieczeń zasobów platformy Azure.

## <a name="view-your-data-security-information"></a>Wyświetlanie informacji o zabezpieczeniach danych

1. W **higieny zabezpieczeń zasobu** kliknij **danych i zasobów magazynowania**.

   ![Zasoby danych i magazynu](./media/security-center-monitoring/click-data.png)

    **Bezpieczeństwo danych** zostanie otwarta strona z zaleceniami dotyczącymi zasobów danych.

     ![Zasoby danych](./media/security-center-monitoring/sql-overview.png)

Na tej stronie możesz wykonywać następujące czynności:

* Kliknij przycisk **Przegląd** karcie znajduje się lista wszystkich zaleceń zasobów danych można skorygować. 
* Kliknij każdą kartę i Wyświetl zalecenia wg typu zasobu.

    > [!NOTE]
    > Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Korygować zalecenia w zasobie danych

1. Za pomocą dowolnego z kart, zasobów kliknij zasób. Zostanie otwarta strona informacji, zaleceń, aby skorygować.

    ![Informacje o zasobach](./media/security-center-monitoring/sql-recommendations.png)

2. Kliknij przycisk rekomendacji. Zostanie otwarta strona zalecenia i wyświetla **czynności zaradczych** wykonania zalecenia.

   ![Instrukcje rozwiązania problemu](./media/security-center-monitoring/remediate1.png)

3. Kliknij przycisk **reakcję**. Zostanie wyświetlona strona Ustawienia zasobów.

    ![Włącz zalecenia](./media/security-center-monitoring/remediate2.png)

4. Postępuj zgodnie z **czynności zaradczych** i kliknij przycisk **Zapisz**.

## <a name="data-and-storage-recommendations"></a>Zalecenia dotyczące danych i magazynu

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Konto magazynu|20|Bezpieczne przesyłanie danych do konta magazynu powinna być włączona.|Bezpieczny transfer to opcja, która wymusza na koncie magazynu w celu umożliwienia akceptowania żądań tylko z bezpiecznego połączenia (HTTPS). HTTPS zapewnia uwierzytelnianie między serwerem i usługi oraz chroni przesyłane dane przed atakami warstwy sieci, takich jak ataki typu man-in--middle, ochronę i przejęcie kontroli sesji.|
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

Aby dowiedzieć się więcej o usłudze Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
