---
title: Karta wersji testowej maszyny wirtualnej w portalu Cloud Partner portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: W tym artykule opisano kartę wersji testowej używanego podczas tworzenia oferty maszyny Wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344626"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta wersji testowej maszyny wirtualnej

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

**Wersji testowej** karcie **nowa oferta** strony umożliwia podanie potencjalnych klientów z praktycznych, przeprowadzanemu samodzielnie procesowi pokaz najważniejsze funkcje i korzyści, przedstawiona w swojego produktu w przypadku scenariusza standardowych.  Wersja testowa to opcjonalna funkcja dla typów ofert, które obsługują wersję testową.  Wersja testowa wymaga obsługi zasobów, aby prawidłowo zaimplementować.  Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace testowej](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aby włączyć tę funkcję, na **wersji testowej** kliknij pozycję **tak** opcja **włączyć wersję testową**.  **Wersji testowej** karty są wyświetlane pola, które są dostępne do edycji.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane.

![Karta dysku testu w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_007.png)

<br/>

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.


|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
|  *Szczegóły*   |  |
| **Opis**           | Zawiera omówienie scenariusza wersji testowej. Ten tekst jest wyświetlany użytkownikowi podczas wersji testowej jest aprowizowana. To pole obsługuje podstawowe HTML, jeśli chcesz zapewnić sformatowaną zawartość.  |
| **Podręcznik użytkownika**           | Przekaż Podręcznik użytkownika szczegółowe (PDF), która umożliwia użytkownikom wersji testowej, zrozumienie, jak użyć rozwiązania.  |
| **Demonstracyjny materiał wideo stacji testu** | Przekaż wideo, w których rozwiązania.  Jeśli wybierzesz tę opcję, musisz podać nazwę, adres URL wideo (w serwisie YouTube lub Vimeo) i miniatury (w pikselach 533 x 324) filmu wideo. |
| *Pomoc konfiguracji* |  |
| **Wystąpienia**             | Określ region, a stosunkowo wystąpienia maszyny wirtualnej (kliknij ikonę informacji, aby uzyskać więcej informacji).  <br/>Potencjalne równoczesnych sesji testowej nie powinna przekraczać limit przydziału dla Twojej subskrypcji.  Pierwsza jest obliczany jako: [liczba regionów zaznaczonego] x [gorąca wystąpień] + [z regionów wybrać numer] x [ciepło wystąpień] + [liczba regionów zaznaczonego] x [zimnych wystąpień] |
| **Test Drive Duration**   | Maksymalny czas trwania sesji czas w godzinach. Sesji testowej kończy się automatycznie po tym okresie zostanie przekroczony.  |
|**Testuj szablon ARM dysku**| Przekaż szablon usługi Azure Resource Manager skojarzonych z tej wersji testowej. Aby uzyskać więcej informacji, zobacz [przekształcania szablonu wdrożenia maszyny wirtualnej dla wersji testowej](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Uzyskiwanie dostępu do informacji**    | Dostęp usługi Azure Resource Manager i informacje logowania wersji próbnej, zapisane jako zwykły tekst lub HTML proste. |
| *Szczegóły subskrypcji wdrożenia dysku testu* |  |
| **Identyfikator subskrypcji platformy Azure** | Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając **subskrypcje** na pasku menu po lewej stronie. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ten identyfikator powinien mieć postać identyfikatora GUID w postaci `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Identyfikator dzierżawy usługi Azure AD**    | Identyfikator usługi Azure Active Directory dzierżawy.  Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając **usługi Azure Active Directory** na pasku menu po lewej stronie, klikając **właściwości** w środkowym pasku menu następnie kopiując **identyfikator katalogu** z formularza.  Ten identyfikator powinny też być identyfikatorem GUID.  Jeśli pole pozostanie puste, należy utworzyć identyfikator dzierżawy dla Twojej organizacji. |
| **Identyfikator aplikacji w usłudze Azure AD**       | Identyfikator rozwiązania zarejestrowanych maszyny Wirtualnej platformy Azure  |
| **Klucz aplikacji w usłudze Azure AD**      | Klucz uwierzytelniania dla zarejestrowanych rozwiązania |
|  |  |

<br/>

W ciągu następnych [Marketplace](./cpp-marketplace-tab.md) karcie będzie zapewniać informacje prawne i marketingu o rozwiązaniu.
