---
title: Karta wersji testowej maszyny wirtualnej w portalu Cloud Partner portalu Azure Marketplace
description: W tym artykule opisano kartę wersji testowej używanego podczas tworzenia oferty maszyny Wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 31c7968d0d96a44ff166444f73807e0ccb5dc583
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938003"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta wersji testowej maszyny wirtualnej

**Wersji testowej** karcie **nowa oferta** strony umożliwia podanie potencjalnych klientów z praktycznych, przeprowadzanemu samodzielnie procesowi pokaz najważniejsze funkcje i korzyści, przedstawiona w swojego produktu w przypadku scenariusza standardowych.  Wersja testowa to opcjonalna funkcja dla typów ofert, które obsługują wersję testową.  Wersja testowa wymaga obsługi zasobów, aby prawidłowo zaimplementować.  Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace testowej](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aby włączyć tę funkcję, na **wersji testowej** kliknij pozycję **tak** opcja **włączyć wersję testową**.  **Wersji testowej** karty są wyświetlane pola, które są dostępne do edycji.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane.

![Karta dysku testu w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_007.png)


## <a name="field-values"></a>Wartości pól

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.  Wymagane pola są wskazanych przez znak gwiazdki (*).


|    Pole                  |       Opis                                                            |
|  ---------                |     ---------------                                                          |
|  *Szczegóły*   |  |
| **Opis elementu\***           | Zawiera omówienie scenariusza wersji testowej. Ten tekst jest wyświetlany użytkownikowi podczas wersji testowej jest aprowizowana. To pole obsługuje podstawowe HTML, jeśli chcesz zapewnić sformatowaną zawartość.  |
| **Podręcznik użytkownika\***           | Przekaż Podręcznik użytkownika szczegółowe (PDF), która umożliwia użytkownikom wersji testowej, zrozumienie, jak użyć rozwiązania.  |
| **Demonstracyjny materiał wideo stacji testu** | Przekaż wideo, w których rozwiązania.  Jeśli wybierzesz tę opcję, musisz podać nazwę, adres URL wideo (w serwisie YouTube lub Vimeo) i miniatury (w pikselach 533 x 324) filmu wideo. |
| *Pomoc konfiguracji* |  |
| **Wystąpienia\***             | Określ region, a stosunkowo wystąpienia maszyny wirtualnej (kliknij ikonę informacji, aby uzyskać więcej informacji).  <br/>Potencjalne równoczesnych sesji testowej nie powinna przekraczać limit przydziału dla Twojej subskrypcji.  Pierwsza jest obliczany jako: [liczba regionów zaznaczonego] x [gorąca wystąpień] + [z regionów wybrać numer] x [ciepło wystąpień] + [liczba regionów zaznaczonego] x [zimnych wystąpień] |
| **Czas trwania testu dysku\***   | Maksymalny czas trwania sesji czas w godzinach. Sesji testowej kończy się automatycznie po tym okresie zostanie przekroczony.  |
|**Testuj szablon ARM dysku\***| Przekaż szablon usługi Azure Resource Manager skojarzonych z tej wersji testowej. Aby uzyskać więcej informacji, zobacz [przekształcania szablonu wdrożenia maszyny wirtualnej dla wersji testowej](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Uzyskiwanie dostępu do informacji\***    | Dostęp usługi Azure Resource Manager i informacje logowania wersji próbnej, zapisane jako zwykły tekst lub HTML proste. |
| *Szczegóły subskrypcji wdrożenia dysku testu* |  |
| **Identyfikator subskrypcji platformy Azure\*** | Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając **subskrypcje** na pasku menu po lewej stronie. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ten identyfikator powinien mieć postać identyfikatora GUID w postaci `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Identyfikator dzierżawy usługi Azure AD\***    | Identyfikator usługi Azure Active Directory dzierżawy.  Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając **usługi Azure Active Directory** na pasku menu po lewej stronie, klikając **właściwości** w środkowym pasku menu następnie kopiując **identyfikator katalogu** z formularza.  Ten identyfikator powinny też być identyfikatorem GUID.  Jeśli pole pozostanie puste, należy utworzyć identyfikator dzierżawy dla Twojej organizacji. |
| **Identyfikator aplikacji w usłudze Azure AD\***       | Identyfikator rozwiązania zarejestrowanych maszyny Wirtualnej platformy Azure  |
| **Klucz aplikacji w usłudze Azure AD\***      | Klucz uwierzytelniania dla zarejestrowanych rozwiązania |
|   |   |


## <a name="next-steps"></a>Kolejne kroki

W ciągu następnych [Marketplace](./cpp-marketplace-tab.md) karcie będzie zapewniać informacje prawne i marketingu o rozwiązaniu.
