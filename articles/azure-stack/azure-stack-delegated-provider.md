---
title: Delegowanie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umieścić innym osobom odpowiedzialnym za tworzenie oferty i założeniem użytkowników.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 161a17f2360767dacc4f418a078c695d7cb8daa7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449750"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegowanie ofert w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Operator usługi Azure Stack często ma być umieszczony innym osobom odpowiedzialnym za tworzenie oferty i rejestracji użytkowników. Na przykład jeśli jesteś dostawcą usługi, możesz zechcieć odsprzedawców, aby zarejestrować się klientów i zarządzanie nimi w Twoim imieniu. Lub, jeśli jesteś częścią centralna grupa IT w przedsiębiorstwie, możesz chcieć oddelegować do logowania użytkownika innymi informatykami.

Delegowanie sprawia, że łatwiej i zarządzania nimi większej liczby użytkowników, które możesz wykonać samodzielnie. Poniższa ilustracja przedstawia jeden poziom obsługi delegowania, ale usługa Azure Stack obsługuje więcej niż jeden poziom. Delegowani dostawcy (DPs) można oddelegować do innych dostawców, maksymalnie pięć poziomów.

![Poziomy delegowania](media/azure-stack-delegated-provider/image1.png)

## <a name="understand-delegation-roles-and-steps"></a>Omówienie delegowania ról i kroki

### <a name="delegation-roles"></a>Delegowanie ról

Następujące role są częścią delegowania:

* *Operatora infrastruktury Azure Stack* zarządza infrastruktury Azure Stack i tworzy szablon oferty. Operator deleguje innym osobom na zapewnienie oferuje użytkownikom.

* Delegowane operatorom usługi Azure Stack są nazywane *delegowane dostawców*. Mogą one należeć do innych organizacji, takich jak inni użytkownicy usługi Azure Active Directory (Azure AD).

* *Użytkownicy* Zamów oferty i używać ich na potrzeby zarządzania ich obciążeń tworzenia maszyn wirtualnych, przechowywania danych i tak dalej.

### <a name="delegation-steps"></a>Delegowanie czynności

Istnieją dwa podstawowe kroki do konfigurowania delegowania:

1. *Utwórz dostawcę delegowanego* subskrypcja użytkownika do oferty zgodnie z planem, zawierający tylko usługi subskrypcji. Użytkownicy, którzy subskrybować tę ofertę można rozszerzyć oferty i logowania użytkowników w przypadku ofert.

1. *Delegowanie oferty delegowanej dostawcy*. Ta oferta jest szablon służący do czego może zaoferować delegowanego dostawca. Delegowani dostawcy można teraz wykonać oferty i zaoferować ją innym użytkownikom.

Na następnym rysunku przedstawiono kroki konfigurowania delegowania.

![Tworzenie dostawcy delegowanego i umożliwia im logowania użytkowników](media/azure-stack-delegated-provider/image2.png)

**Wymagania dotyczące delegowanych dostawców**

Do działania jako dostawca delegowanego, użytkownik musi ustanowić relację z głównym dostawcą podczas tworzenia subskrypcji. Ta subskrypcja identyfikuje delegowani dostawcy jako prawo do obecnych ofert w imieniu głównego dostawcy.

Po ustanowieniu tej relacji operatora infrastruktury Azure Stack można delegować oferty delegowanej dostawcy. Delegowani dostawcy może potrwać oferty, zmień jej nazwę (ale nie zmieniać jego substancji) i oferować je swoim klientom.

## <a name="delegation-walkthrough"></a>Przewodnik delegowania

Praktyczne wskazówki Konfigurowanie delegowanych dostawcę, delegowanie oferty i weryfikowanie, czy użytkownicy mogą zarejestrować się do delegowaną ofertę można znaleźć w poniższych sekcjach.

### <a name="set-up-roles"></a>Konfigurowanie ról

Aby użyć tego przewodnika, potrzebujesz dwóch kont usługi Azure AD oprócz konta usługi Azure Stack — operator. Jeśli nie masz tych dwóch kont, należy je utworzyć. Konta mogą należeć do dowolnego użytkownika usługi Azure AD i są określane jako dostawca delegowanego i użytkownika.

| **Rola** | **Uprawnienia organizacji** |
| --- | --- |
| Delegowani dostawcy |Użytkownik |
| Użytkownik |Użytkownik |

### <a name="identify-the-delegated-provider"></a>Identyfikowanie delegowani dostawcy

1. Zaloguj się do portalu administratora jako operatorów usługi Azure Stack.

1. Aby utworzyć ofertę, która umożliwia użytkownikowi stają się delegowani dostawcy:

   a.  [Utwórz plan](azure-stack-create-plan.md).
       Ten plan powinien obejmować usługę subskrypcji. W tym artykule używany plan o nazwie **PlanForDelegation** jako przykład.

   b.  [Utwórz ofertę](azure-stack-create-offer.md) na podstawie tego planu. W tym artykule wykorzystano oferty o nazwie **OfferToDP** jako przykład.

   c.  Dodaj dostawcę delegowanego jako subskrybenta tej oferty, wybierając **subskrypcje** > **Dodaj** > **nowej subskrypcji dzierżawcy**.

   ![Dodaj dostawcę delegowanego jako subskrybenta](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > W przypadku wszystkich ofert usługi Azure Stack, jeśli masz możliwość dokonywania oferty publicznej i dzięki czemu użytkownicy zasubskrybować, ani jej zachowanie prywatne i umożliwienie operatora infrastruktury Azure Stack Zarządzanie rejestracji. Delegowani dostawcy są zwykle małe grupy. Użytkownik chce kontrolować, kto dopuszczone do niego, aby zachować poufność ta oferta ma sens w większości przypadków.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Operator usługi Azure Stack tworzy delegowaną ofertę

Następnym krokiem jest, aby utworzyć plan i ofertę, które zamierzasz przekazać i będą używać użytkownicy. To dobry pomysł, aby zdefiniować tę ofertę, dokładnie tak, jak użytkownicy, aby zobaczyć, że ponieważ delegowani dostawcy nie można zmienić planów i przydziałów, które zawiera.

1. Jako operatorów usługi Azure Stack [Utwórz plan](azure-stack-create-plan.md) i [oferty](azure-stack-create-offer.md) zgodnie z planem. W tym artykule wykorzystano oferty o nazwie **DelegatedOffer** jako przykład.

   > [!NOTE]
   > Ta oferta nie musi być publiczne, ale możesz przekształcić ją w publicznych, jeśli chcesz. Jednak w większości przypadków mają tylko delegowanych dostawców na dostęp do tej oferty. Po można delegować oferty prywatne, zgodnie z opisem w poniższych krokach, delegowani dostawcy ma do niego dostęp.

1. Delegowanie oferty. Przejdź do **DelegatedOffer**. W obszarze **ustawienia**, wybierz opcję **delegowane dostawców** > **Dodaj**.

1. Wybierz subskrypcję dla delegowanego dostawcy z listy rozwijanej, a następnie wybierz pozycję **delegata**.

   ![Dodaj dostawcę delegowanego](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Delegowani dostawcy dostosowuje oferty

Zaloguj się do portalu użytkownika jako dostawca delegowanego, a następnie utwórz nową ofertę za pomocą delegowaną ofertę jako szablon.

1. Wybierz **nowe** > **dzierżawy oferty i plany** > **oferują**.

    ![Utwórz nową ofertę](media/azure-stack-delegated-provider/image5.png)

1. Przypisz nazwę do tej oferty. W tym artykule wykorzystano **ResellerOffer** jako przykład. Wybierz delegowaną ofertę, na którym chcesz utworzyć ją, a następnie wybierz **Utwórz**.

   ![Przypisz nazwę](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Należy zrozumieć, w odróżnieniu od operatorów usługi Azure Stack delegowani dostawcy nie konstruowania oferty z planów podstawowych i planów dodatków. Delegowani dostawcy można wybrać tylko oferty, które mają delegowane do nich, nie można wprowadzać zmian tych ofert.

1. Upublicznić oferty, wybierając **Przeglądaj**, a następnie **oferuje**. Wybierz ofertę, a następnie wybierz **zmiany stanu**.

1. Delegowani dostawcy udostępnia te oferty za pośrednictwem ich własnych portalu adresu URL. Te oferty są widoczne wyłącznie za pośrednictwem portalu delegowanego. Aby znaleźć i zmienić tego adresu URL:

    a.  Wybierz **Przeglądaj** > **więcej usług** > **subskrypcje**. Następnie wybierz delegowaną subskrypcję dostawcy. Na przykład **DPSubscription** > **właściwości**.

    b.  Skopiuj portalu adres URL do innej lokalizacji, takiego jak Notatnik.

    ![Wybierz delegowaną subskrypcję dostawcy](media/azure-stack-delegated-provider/dpportaluri.png)  

   Utworzeniu delegowaną ofertę jako delegowani dostawcy. Wyloguj się jako dostawca delegowanego, a następnie zamknij okno przeglądarki, z którego korzystasz.

### <a name="sign-up-for-the-offer"></a>Utwórz konto dla oferty

1. W nowym oknie przeglądarki przejdź do portalu delegowanego adresu URL, który został zapisany w poprzednim kroku. Zaloguj się do portalu jako użytkownik.

   >[!NOTE]
   >Delegowane oferty nie są widoczne, chyba że za pomocą delegowanego portalu.

1. Na pulpicie nawigacyjnym wybierz **Uzyskaj subskrypcję**. Zobaczysz, że tylko delegowane oferty, które zostały utworzone przez dostawcę delegowane są prezentowane użytkownikowi.

   ![Wyświetlać i wybierać oferty](media/azure-stack-delegated-provider/image8.png)

Delegowanie oferty proces jest ukończona. Teraz użytkownik może zarejestrować się dla tej oferty, pobieranie subskrypcji.

## <a name="multiple-tier-delegation"></a>Delegowanie n warstwowych

N warstwowych delegowanie umożliwia delegowani dostawcy można delegować oferty do innych jednostek. Na przykład aby utworzyć bardziej odsprzedawcy kanałów where:

* Dostawcy, który zarządza usługi Azure Stack deleguje ofertę dystrybutora.
* Delegaty dystrybutora odsprzedawcy.

Aby utworzyć wiele warstw Delegowanie oferty, delegowani dostawcy deleguje oferty do następnego dostawcy. Proces jest taki sam dla delegowanego dostawcy, jak w przypadku operatora infrastruktury Azure Stack. Aby uzyskać więcej informacji, zobacz [operatora infrastruktury Azure Stack tworzy delegowaną ofertę](#cloud-operator-creates-the-delegated-offer).

## <a name="next-steps"></a>Kolejne kroki

[Aprowizowanie maszyny Wirtualnej](azure-stack-provision-vm.md)