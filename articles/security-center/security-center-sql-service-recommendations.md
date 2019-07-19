---
title: Ochrona danych i usług magazynu platformy Azure w Azure Security Center | Microsoft Docs
description: Ten dokument zawiera zalecenia dotyczące Azure Security Center, które pomagają chronić dane oraz usługę Azure SQL i zachować zgodność z zasadami zabezpieczeń.
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
ms.author: v-mohabe
ms.openlocfilehash: 9e48114d0d4159d40006710f9c8194dea0d775f8
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295629"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Ochrona usług Azure Data and Storage w Azure Security Center
W tym temacie pokazano, jak wyświetlać i implementować zalecenia dotyczące zabezpieczeń danych i zasobów magazynu. Azure Security Center te zalecenia podczas analizowania stanu zabezpieczeń zasobów platformy Azure.

## <a name="view-your-data-security-information"></a>Wyświetlanie informacji o zabezpieczeniach danych

1. W sekcji **higiena zabezpieczeń zasobów** kliknij pozycję **zasoby danych i magazyn**.

   ![Zasoby magazynu danych &](./media/security-center-monitoring/click-data.png)

    Zostanie otwarta strona **zabezpieczenia danych** z zaleceniami dotyczącymi zasobów danych.

     ![Zasoby danych](./media/security-center-monitoring/sql-overview.png)

Na tej stronie można:

* Kliknij kartę **Przegląd** , aby wyświetlić listę wszystkich zaleceń dotyczących zasobów danych do skorygowania. 
* Kliknij każdą kartę i przejrzyj zalecenia według typu zasobu.

    > [!NOTE]
    > Aby uzyskać więcej informacji dotyczących szyfrowania magazynu, przeczytaj artykuł [Enable encryption for Azure storage account in Azure Security Center](security-center-enable-encryption-for-storage-account.md) (Włączanie szyfrowania dla konta usługi Azure Storage w usłudze Azure Security Center).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Korygowanie zalecenia dotyczącego zasobu danych

1. Na dowolnych kartach zasobów kliknij zasób. Zostanie otwarta strona informacje z listą zaleceń, które należy skorygować.

    ![Informacje o zasobach](./media/security-center-monitoring/sql-recommendations.png)

2. Kliknij zalecenie. Zostanie otwarta strona rekomendacja i zostanie wyświetlona **procedura korygowania** służąca do zaimplementowania zalecenia.

   ![Kroki zaradcze](./media/security-center-monitoring/remediate1.png)

3. Kliknij przycisk **podejmij akcję**. Zostanie wyświetlona strona Ustawienia zasobów.

    ![Włącz rekomendację](./media/security-center-monitoring/remediate2.png)

4. Wykonaj **czynności zaradcze** i kliknij przycisk **Zapisz**.

## <a name="data-and-storage-recommendations"></a>Zalecenia dotyczące danych i magazynu

|Typ zasobu|Wskaźnik bezpieczeństwa|Zalecenie|Opis|
|----|----|----|----|
|Konto magazynu|20|Należy włączyć bezpieczny transfer do kont magazynu|Bezpieczny transfer to opcja, która wymusza akceptowanie przez konto magazynu żądań tylko z bezpiecznych połączeń (HTTPS). Protokół HTTPS zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|
|Redis|20|Należy włączyć tylko bezpieczne połączenia z Redis Cache|Włącz tylko połączenia za pośrednictwem protokołu SSL do usługi Azure cache for Redis. Użycie bezpiecznych połączeń zapewnia uwierzytelnianie między serwerem a usługą i chroni dane przesyłane przed atakami z warstwy sieci, takimi jak ataki typu man-in-the-Middle, podsłuchiwanie i przejmowanie sesji.|
|SQL|15|Należy włączyć Transparent Data Encryption baz danych SQL|Włącz przezroczyste szyfrowanie danych, aby chronić dane w czasie spoczynku i spełnić wymagania dotyczące zgodności.|
|SQL|15|Inspekcja programu SQL Server powinna być włączona|Włącz inspekcję dla serwerów SQL platformy Azure. (Tylko usługa Azure SQL. Nie zawiera kodu SQL uruchomionego na maszynach wirtualnych.|
|Data Lake Analytics|5|Dzienniki diagnostyczne w Data Lake Analytics powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|Data Lake — magazyn|5|Dzienniki diagnostyczne w Azure Data Lake Store powinny być włączone|Włączanie dzienników i zachowują je nawet przez rok. Dzięki temu można ponownie utworzyć dzienników aktywności na potrzeby analizy, gdy wystąpi zdarzenie naruszenia zabezpieczeń lub złamania zabezpieczeń sieci. |
|SQL|30|Luki w zabezpieczeniach baz danych SQL należy skorygować|Ocena luk w zabezpieczeniach SQL skanuje bazę danych pod kątem luk w zabezpieczeniach i ujawnia wszelkie odchylenia od najlepszych rozwiązań, takich jak niepotrzebne konfiguracje, nadmierne uprawnienia i niechronione dane poufne. Rozpoznanie znalezionych luk w zabezpieczeniach może znacznie ulepszyć schemacie zabezpieczeń bazy danych.|
|SQL|20|Inicjowanie obsługi administracyjnej administratora usługi Azure AD dla programu SQL Server|Aby włączyć uwierzytelnianie usługi Azure AD, Udostępnij administratorowi usługi Azure AD dla programu SQL Server. Uwierzytelnianie usługi Azure AD umożliwia uproszczone zarządzanie uprawnieniami oraz scentralizowane zarządzanie tożsamościami użytkowników baz danych i innych usług firmy Microsoft.|
|Konto magazynu|15|Dostęp do kont magazynu z konfiguracją zapory i sieci wirtualnej należy ograniczyć|Inspekcja nieograniczonego dostępu do sieci w ustawieniach zapory konta magazynu. Zamiast tego należy skonfigurować reguły sieciowe, tak aby tylko aplikacje z dozwolonych sieci mogły uzyskiwać dostęp do konta magazynu. Aby zezwolić na połączenia z określonych klientów internetowych lub lokalnych, można udzielić dostępu do ruchu z określonych sieci wirtualnych platformy Azure lub do publicznych zakresów adresów IP w sieci Internet.|
|Konto magazynu|1|Konta magazynu należy migrować do nowych zasobów Azure Resource Manager|Użyj nowego Azure Resource Manager V2 dla kont magazynu w celu zapewnienia ulepszeń zabezpieczeń, takich jak: silniejsza kontrola dostępu (RBAC), lepsza Inspekcja, wdrażanie i zarządzanie oparte na Menedżer zasobówach, dostęp do zarządzanych tożsamości, dostęp do magazynu kluczy dla wpisy tajne i uwierzytelnianie oparte na usłudze Azure AD oraz obsługa tagów i grup zasobów w celu łatwiejszego zarządzania zabezpieczeniami.|

## <a name="see-also"></a>Zobacz także
Aby dowiedzieć się więcej o zaleceniach dotyczących innych typów zasobów platformy Azure, zobacz następujące tematy:

* [Ochrona maszyn wirtualnych w usłudze Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrona aplikacji w usłudze Azure Security Center](security-center-application-recommendations.md)
* [Ochrona sieci w usłudze Azure Security Center](security-center-network-recommendations.md)

Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
