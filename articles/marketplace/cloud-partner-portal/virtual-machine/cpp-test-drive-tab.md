---
title: Karta dysk testowy maszyny wirtualnej w portal Cloud Partner portalu Azure Marketplace
description: Opisuje kartę dysk testowy służącą do tworzenia oferty maszyny wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808875"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta Stacja testowa maszyn wirtualnych

Karta **Stacja testowa** na stronie **Nowa oferta** umożliwia klientom potencjalnych klientów korzystanie z praktycznego pokazu najważniejszych funkcji i korzyści Twojego produktu, które przedstawiono w ustandaryzowanym scenariuszu.  Test Drive to opcjonalna funkcja dla typów ofert, które obsługują test na dysku.  Wersja testowa wymaga prawidłowego wdrożenia zasobów pomocniczych.  Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Aby włączyć tę funkcję, na karcie **dysk testowy** kliknij opcję **tak** , aby **włączyć dysk testowy**.  Na karcie **dysk testowy** są wyświetlane pola dostępne do edycji.  Dołączona gwiazdka (*) w nazwie pola wskazuje, że jest to wymagane.

![Karta Stacja testowa w formularzu Nowa oferta dla maszyn wirtualnych](./media/publishvm_007.png)


## <a name="field-values"></a>Wartości pól

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.  Wymagane pola są indicted przez gwiazdkę (*).


|    Pole                  |       Opis                                                            |
|  ---------                |     ---------------                                                          |
|  *Szczegóły*   |  |
| **\* opisu**           | Zadbaj o to, aby zapoznać się z omówieniem scenariusza testowego. Ten tekst będzie widoczny dla użytkownika podczas aprowizacji dysku testowego. To pole obsługuje podstawowy kod HTML, jeśli chcesz udostępnić sformatowaną zawartość.  |
| **Ręczne\* użytkownika**           | Przekaż szczegółowy podręcznik użytkownika (. PDF), który pomaga testować, jak korzystać z rozwiązania.  |
| **Wideo demonstracyjne na dysku testowym** | Przekaż wideo, które przedstawia Twoje rozwiązanie.  W przypadku wybrania tej opcji należy podać nazwę, adres URL wideo (hostowane w serwisie YouTube lub Vimeo) oraz miniaturę (533x324 pikseli) dla filmu wideo. |
| *Konfiguracja techniczna* |  |
| **\* wystąpień**             | Określ dostępność regionów i stosunkowo dostęp do wystąpienia maszyny wirtualnej (kliknij ikonę informacji, aby uzyskać więcej informacji).  <br/>W przypadku potencjalnych współbieżnych sesji dysków testowych nie należy przekroczyć limitu przydziału dla subskrypcji.  Dawniej jest obliczony jako: [liczba wybranych regionów] x [wystąpienia aktywne] + [wybrane regiony] x [rozgrzane wystąpienia] + [liczba wybranych regionów] x [zimne wystąpienia] |
| **Czas trwania dysku testowego\***   | Maksymalny czas trwania sesji w godzinach. Sesja dysku testowego kończy się automatycznie po przekroczeniu tego okresu.  |
|**\* szablonu ARM na dysku testowym**| Przekaż szablon Azure Resource Manager skojarzony z tym dyskiem testowym. Aby uzyskać więcej informacji, zobacz [transformacjeing a Virtual Machine Deployment Template for Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **\* informacji o dostępie**    | Azure Resource Manager dostęp do informacji o logowaniu i wersji próbnej, zapisaną jako zwykły tekst lub prosty kod HTML. |
| *Szczegóły subskrypcji wdrożenia dysku testowego* |  |
| **Identyfikator subskrypcji platformy Azure\*** | Można uzyskać, logując się do [Microsoft Azure Portal](https://ms.portal.azure.com) i klikając pozycję **subskrypcje** na lewym pasku menu. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Ten identyfikator powinien być identyfikatorem GUID formularza `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Identyfikator dzierżawy usługi Azure AD\***    | Azure Active Directory identyfikator dzierżawy.  Można uzyskać, logując się do [Microsoft Azure Portal](https://ms.portal.azure.com) i klikając przycisk **Azure Active Directory** na lewym pasku menu, a następnie klikając pozycję **Właściwości** na środkowym pasku menu, a następnie kopiując **Identyfikator katalogu** z formularza.  Ten identyfikator powinien być również identyfikatorem GUID.  Jeśli to pole jest puste, należy utworzyć identyfikator dzierżawy dla swojej organizacji. |
| **Identyfikator aplikacja usługi Azure AD\***       | Identyfikator zarejestrowanego rozwiązania maszyny wirtualnej platformy Azure  |
| **\* klucza aplikacja usługi Azure AD**      | Klucz uwierzytelniania dla zarejestrowanego rozwiązania |
|   |   |


## <a name="next-steps"></a>Następne kroki

Na następnej karcie [Marketplace](./cpp-marketplace-tab.md) znajdziesz informacje marketingowe i prawne dotyczące Twojego rozwiązania.
