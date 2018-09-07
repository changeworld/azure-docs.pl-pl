---
title: Delegowanie oferty w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umieścić innym osobom odpowiedzialnym za tworzenie oferty i założeniem użytkowników.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: b58118a3ed4fba5017e0eb630d822ec59f04aa16
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024780"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegowanie ofert w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Operator usługi Azure Stack często ma być umieszczony innym osobom odpowiedzialnym za podpisywanie użytkowników i tworzenia subskrypcji. Na przykład jeśli jesteś dostawcą usługi, możesz zechcieć odsprzedawców, aby zarejestrować się klientów i zarządzanie nimi w Twoim imieniu. Lub, jeśli jesteś częścią centralna grupa IT w przedsiębiorstwie, możesz chcieć oddelegować do logowania użytkownika innymi informatykami.

Delegowanie sprawia, że łatwiej i zarządzania nimi większej liczby użytkowników, które możesz wykonać samodzielnie, jak pokazano na poniższej ilustracji. 

![Poziomy delegowania](media/azure-stack-delegated-provider/image1.png)

Delegowanie dostawca delegowanego zarządza oferty (delegowaną ofertę) i klientów końcowych uzyskać subskrypcje w ramach tej oferty, bez korzystania z administratorem systemu. 

## <a name="understand-delegation-roles-and-steps"></a>Omówienie delegowania ról i kroki

### <a name="delegation-roles"></a>Delegowanie ról

Następujące role są częścią delegowania:

* *Operatora infrastruktury Azure Stack* zarządza infrastruktury Azure Stack i tworzy szablon oferty. Operator deleguje innych użytkowników, aby zapewnić oferty z ich dzierżawy.

* Delegowane operatorom usługi Azure Stack to użytkownicy z *właściciela* lub *Współautor* prawa w ramach subskrypcji o nazwie *delegowane dostawców*. Mogą one należeć do innych organizacji, takich jak innych dzierżaw usługi Azure Active Directory (Azure AD).

* *Użytkownicy* Zamów oferty i używać ich na potrzeby zarządzania ich obciążeń tworzenia maszyn wirtualnych, przechowywania danych i tak dalej.

### <a name="delegation-steps"></a>Delegowanie czynności

Istnieją dwa podstawowe kroki do konfigurowania delegowania:

1. *Utwórz subskrypcję dostawcy delegowane* , subskrybując ten biuletyn użytkownikowi oferty zawierające tylko usługi subskrypcji. Użytkownicy, którzy subskrybować tę ofertę następnie rozszerzyć delegowanych ofert dla innych użytkowników, rejestrując je dla tych ofert.

2. *Delegowanie oferty delegowanej dostawcy*. Ta oferta umożliwia delegowani dostawcy do utworzenia subskrypcji lub rozszerzenie oferty do użytkowników. Delegowani dostawcy można teraz wykonać oferty i zaoferować ją innym użytkownikom.

Na następnym rysunku przedstawiono kroki konfigurowania delegowania.

![Tworzenie dostawcy delegowanego i umożliwia im logowania użytkowników](media/azure-stack-delegated-provider/image2.png)

**Wymagania dotyczące delegowanych dostawców**

Do działania jako dostawca delegowanego, użytkownik musi ustanowić relację z głównym dostawcą podczas tworzenia subskrypcji. Ta subskrypcja identyfikuje delegowani dostawcy jako mające uprawnienia do przedstawienia delegowanych ofert w imieniu głównego dostawcy.

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
   >Jest ważne dowiedzieć się, że delegowanych dostawców można wybrać tylko oferty, które mają delegowane do nich. Nie mogą oni wprowadzić zmiany do tych ofert. Operator tylko do usługi Azure Stack można zmienić tych ofert, na przykład, zmieniając ich planów i przydziałów. Delegowani dostawcy nie konstruowania oferty z planów podstawowych i planów dodatków. 

3. Delegowani dostawcy można upublicznić tych ofert za pośrednictwem ich własnych portalu adresu URL. Aby upublicznić oferty, wybierz pozycję **Przeglądaj**, a następnie **oferuje**. Wybierz ofertę, a następnie wybierz **zmiany stanu**.

4. Publiczne delegowane oferty są teraz widoczne wyłącznie za pośrednictwem portalu delegowanego. Aby znaleźć i zmienić tego adresu URL:

    a.  Wybierz **Przeglądaj** > **wszystkich usług**, a następnie w obszarze **ogólne** kategorii, wybierz opcję **subskrypcje**. Wybierz delegowaną subskrypcję dostawcy. Na przykład **DPSubscription** > **właściwości**.

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

## <a name="move-subscriptions-between-delegated-providers"></a>Przenoszenie subskrypcji delegowanych dostawców

Jeśli to konieczne, można przenieść subskrypcję między subskrypcjami nowej lub istniejącej delegowani dostawcy, które należą do tej samej dzierżawie katalogu. To za pomocą polecenia cmdlet programu PowerShell [AzsSubscription przenoszenia](https://docs.microsoft.com/powershell/module/azs.subscriptions.admin).

To jest przydatne, gdy:
- Dołączanie nowego członka zespołu, prowadzące w roli dostawcy delegowanego i chcesz przypisać do tego zespołu elementu członkowskiego-subskrypcji użytkownika, które zostały utworzone wcześniej w ramach subskrypcji dostawcy domyślne.
- Możesz mieć wiele subskrypcji delegowanych dostawców w tej samej dzierżawie katalogu.-(Azure Active Directory) i konieczne przeniesienie subskrypcji użytkownika między nimi. Może to być wtedy, gdy członek zespołu przechodzi między zespołami i ich subskrypcji musi być przydzielona do nowego zespołu.


## <a name="next-steps"></a>Kolejne kroki

[Aprowizowanie maszyny Wirtualnej](azure-stack-provision-vm.md)