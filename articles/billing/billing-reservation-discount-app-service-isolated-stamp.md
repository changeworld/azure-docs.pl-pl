---
title: Jak są stosowane rabaty rezerwacji do Azure App Service wyizolowanych sygnatur
description: Dowiedz się, w jaki sposób rabaty rezerwacji dotyczą Azure App Service wyizolowanych sygnatur.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298258"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Jak są stosowane rabaty rezerwacji do Azure App Service wyizolowanych sygnatur

Po zakupie zarezerwowanych pojemności opłaty za sygnaturę izolowana usługa App Serviceu rabat zostanie automatycznie zastosowany do opłaty za sygnaturę w regionie. Rabat związany z rezerwacją dotyczy użycia emitowanego przez wskaźnik odizolowanej sygnatury. Pracownicy, dodatkowe frontony i inne zasoby związane z sygnaturą nadal są rozliczane według zwykłej stawki.

## <a name="reservation-discount-application"></a>Aplikacja rabatu rezerwacji

Rabat opłaty za pieczęć izolowana usługa App Service jest stosowany do uruchamiania wyizolowanych znaczków co godzinę. Jeśli nie masz zastosowanej sygnatury na godzinę, zarezerwowana pojemność jest tracona dla tej godziny. Nie jest przenoszone.

Po zakupie rezerwacja zakupu jest dopasowywana do izolowanej sygnatury działającej w określonym regionie. Jeśli wyłączysz tę sygnaturę, rabaty rezerwacji są automatycznie stosowane do wszelkich innych sygnatur w regionie. Gdy nie istnieją żadne sygnatury, rezerwacja zostanie zastosowana do następnej sygnatury, która została utworzona w regionie.

Gdy sygnatury nie są uruchamiane przez pełną godzinę, rezerwacja automatycznie stosuje się do innych zgodnych sygnatur w tym samym regionie w tej samej godzinie.

## <a name="choose-a-stamp-type---windows-or-linux"></a>Wybieranie typu sygnatury — Windows lub Linux

Pusta sygnatura izolowana domyślnie emituje licznik sygnatur systemu Windows. Na przykład, gdy nie wdrożono procesów roboczych. W dalszym ciągu emituje licznik podczas wdrażania procesów roboczych systemu Windows. Licznik jest zmieniany na miernik sygnatury systemu Linux w przypadku wdrożenia procesu roboczego systemu Linux. Sygnatura emituje licznik systemu Windows podczas wdrażania zarówno procesów roboczych z systemem Linux, jak i Windows.

W związku z tym licznik sygnatur może ulec zmianie między oknami i Linux w czasie trwania sygnatury. Tymczasem rezerwacje są specyficzne dla systemu operacyjnego. Musisz kupić rezerwację, która obsługuje procesy robocze, które planujesz wdrożyć do sygnatury. Tylko system Windows sygnatury i sygnatury mieszane używają rezerwacji systemu Windows. Sygnatury z tylko pracownikami systemu Linux używają rezerwacji systemu Linux.

Jedyną koniecznością zakupu rezerwacji systemu Linux jest zaplanowanie _tylko_ procesów roboczych systemu Linux w sygnaturze.

## <a name="discount-examples"></a>Przykłady rabatu

W poniższych przykładach pokazano, jak ma zastosowanie rabat oddzielonej opłaty za wystawkę za wystąpienie zarezerwowane, w zależności od wdrożeń.

- **Przykład 1**: Zakup jednego wystąpienia izolowanej zastrzeżonej sygnatury w regionie bez sygnatur izolowana usługa App Service. Wdrażasz nową sygnaturę w regionie i płacisz stawki zarezerwowane dla tej sygnatury.
- **Przykład 2**: Zakup jednego wystąpienia izolowanej zarezerwowanej sygnatury w regionie, w którym już zainstalowano sygnaturę izolowana usługa App Service. Zaczynasz otrzymywać zarezerwowaną stawkę dla wdrożonej sygnatury.
- **Przykład 3**: Zakup jednego wystąpienia izolowanej zastrzeżonej sygnatury w regionie z już wdrożoną sygnaturą izolowana usługa App Service. Zaczynasz otrzymywać zastrzeżoną stawkę od wdrożonej sygnatury. Później należy usunąć sygnaturę i wdrożyć nową. Zostanie wyświetlona zastrzeżona stawka dla nowej sygnatury. Zniżki nie są przenoszone przez czasy trwania bez wdrożonych sygnatur.
- **Przykład 4**: Zakup jednego wystąpienia izolowanej zarezerwowanej sygnatury systemu Linux w regionie powoduje wdrożenie nowej sygnatury w regionie. Gdy sygnatura jest początkowo wdrażana bez procesów roboczych, emituje licznik sygnatur systemu Windows. Nie otrzymano żadnego rabatu. Gdy pierwszy proces roboczy systemu Linux zostanie wdrożony sygnatura, emituje miernik sygnatury systemu Linux i ma zastosowanie rabat rezerwacji. Jeśli proces roboczy systemu Windows zostanie później wdrożony w sygnaturze, licznik sygnatury wraca do systemu Windows. Nie otrzymujesz już zniżki dotyczącej rezerwacji oddzielonej sygnatury systemu Linux.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak zarządzać rezerwacją, zobacz [zarządzanie Azure Reservations](billing-manage-reserved-vm-instance.md).
- Aby dowiedzieć się więcej na temat wstępnej zakupu izolowana usługa App Service zarezerwowanych zdolności do oszczędzania pieniędzy, zapoznaj się z tematem przedpłaty za [Azure App Servicee opłaty](billing-prepay-app-service-isolated-stamp.md)za sygnaturę oddzieloną
- Aby dowiedzieć się więcej na temat Azure Reservations, zobacz następujące artykuły:
  - [Co to są Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Zarządzanie rezerwacjami na platformie Azure](billing-manage-reserved-vm-instance.md)
  - [Poznaj użycie rezerwacji dla subskrypcji z użyciem stawek płatność zgodnie z rzeczywistym zużyciem](billing-understand-reserved-instance-usage.md)
  - [Informacje na temat użycia rezerwacji na potrzeby rejestracji w przedsiębiorstwie](billing-understand-reserved-instance-usage-ea.md)
