---
title: Karta Dysk testowy maszyny wirtualnej w portalu partnerów w chmurze dla portalu azure marketplace
description: W tym artykule opisano kartę Dysku testowego używanego do tworzenia oferty maszyny wirtualnej w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 22538047adc17a40438359e11bff7fd20e43bcc6
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273090"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta Dysk testowy maszyny wirtualnej

> [!IMPORTANT]
> Od 13 kwietnia 2020 r. rozpoczniemy przenoszenie zarządzania ofertami maszyny wirtualnej platformy Azure do centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [tworzenie oferty maszyny wirtualnej platformy Azure,](https://aka.ms/CreateAzureVMoffer) aby zarządzać zmigrowanymi ofertami.

Karta **Test Drive** na stronie Nowa **oferta** umożliwia zapewnienie potencjalnym klientom praktycznej, samodzielnej demonstracji kluczowych funkcji i zalet produktu, zademonstrowanej w znormalizowanym scenariuszu.  Test Drive jest opcjonalną funkcją dla typów ofert, które obsługują jazdę próbną.  Test drive wymaga prawidłowego wdrożenia zasobów pomocniczych.  Aby uzyskać więcej informacji, zobacz artykuł [Usługa Testowa portalu Azure Marketplace](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aby włączyć tę funkcję, na karcie **Dysk testowy** kliknij opcję **Tak** w **obszarze Włącz jazdę próbną**.  Na karcie **Dysk testowy** zostaną wyświetlone pola dostępne do edycji.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest ona wymagana.

![Karta Dysk testowy w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_007.png)


## <a name="field-values"></a>Wartości pól

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.  Wymagane pola są oskarżane gwiazdką (*).


|    Pole                  |       Opis                                                            |
|  ---------                |     ---------------                                                          |
|  *Szczegóły*   |  |
| **Opis\***           | Podaj przegląd scenariusza jazdy testowej. Ten tekst będzie wyświetlany użytkownikowi podczas obsługi administracyjnej dysku testowego. To pole obsługuje podstawowy kod HTML, jeśli chcesz podać sformatowaną zawartość.  |
| **Instrukcja obsługi\***           | Prześlij szczegółową instrukcję obsługi (.pdf), która pomaga użytkownikom testów zrozumieć, jak korzystać z rozwiązania.  |
| **Film demonstracyjny dysku testowego** | Prześlij film, który prezentuje Twoje rozwiązanie.  Jeśli wybierzesz tę opcję, musisz podać nazwę, adres URL filmu (hostowany w YouTube lub Vimeo) oraz miniaturę filmu o rozmiarze (533x324 pikseli). |
| *Konfiguracja techniczna* |  |
| **Wystąpień\***             | Określ dostępność regionu i stosunkowo dostępność wystąpienia maszyny wirtualnej (kliknij ikonę informacji, aby uzyskać więcej szczegółów).  <br/>Potencjalne równoczesnych sesji dysku testowego nie powinny przekraczać limitu przydziału dla subskrypcji.  Pierwsza jest obliczana jako: [Liczba wybranych regionów] x [Gorące wystąpienia] + [Wybrana liczba regionów] x [Ciepłe wystąpienia] + [Wybrana liczba regionów] x [Zimne wystąpienia] |
| **Czas trwania jazdy próbna\***   | Maksymalny czas trwania sesji w godzinach. Sesja dysku testowego kończy się automatycznie po przekroczeniu tego okresu.  |
|**Szablon ARM dysku testowego\***| Przekaż szablon usługi Azure Resource Manager skojarzony z tym dyskiem testowym. Aby uzyskać więcej informacji, zobacz [Przekształcanie szablonu wdrażania maszyny wirtualnej dla dysku testowego](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Informacje o dostępie\***    | Dostęp do usługi Azure Resource Manager i próbne informacje logowania, napisane jako zwykły tekst lub prosty kod HTML. |
| *Szczegóły subskrypcji wdrożenia dysku testowego* |  |
| **Identyfikator subskrypcji platformy Azure\*** | Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając **subskrypcje** na lewym pasku menu. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ten identyfikator powinien być identyfikatorem `a83645ac-1234-5ab6-6789-1h234g764ghty`GUID formularza .|
| **Identyfikator dzierżawy usługi Azure AD\***    | Identyfikator dzierżawy usługi Azure Active Directory.  Można uzyskać, logując się do [portalu Microsoft Azure](https://ms.portal.azure.com) i klikając usługę Azure Active **Directory** na lewym pasku menu, a następnie klikając polecenie Właściwości w **środkowym** pasku menu, a następnie kopiując **identyfikator katalogu** z formularza.  Ten identyfikator powinien być również identyfikatorem GUID.  Jeśli jest puste, należy utworzyć identyfikator dzierżawy dla organizacji. |
| **Identyfikator aplikacji usługi Azure AD\***       | Identyfikator zarejestrowanego rozwiązania maszyny Wirtualnej platformy Azure  |
| **Klucz aplikacji usługi Azure AD\***      | Klucz uwierzytelniania zarejestrowanego rozwiązania |
|   |   |


## <a name="next-steps"></a>Następne kroki

W następnej karcie [Marketplace](./cpp-marketplace-tab.md) podasz informacje marketingowe i prawne dotyczące rozwiązania.
