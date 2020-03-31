---
title: Tworzenie lub edytowanie grupy dynamicznej i uzyskanie statusu — Azure AD | Dokumenty firmy Microsoft
description: Jak utworzyć lub zaktualizować regułę członkostwa w grupie w witrynie Azure portal i sprawdzić jej stan przetwarzania.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266379"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Tworzenie lub aktualizowanie grupy dynamicznej w usłudze Azure Active Directory

W usłudze Azure Active Directory (Azure AD) można używać reguł do określania członkostwa w grupie na podstawie właściwości użytkownika lub urządzenia. W tym artykule opisano, jak skonfigurować regułę dla grupy dynamicznej w witrynie Azure portal.
Członkostwo dynamiczne jest obsługiwane dla grup zabezpieczeń lub grup usługi Office 365. Po zastosowaniu reguły członkostwa w grupie atrybuty użytkownika i urządzenia są oceniane pod kątem dopasowania do reguły członkostwa. Gdy atrybut zmienia się dla użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmian członkostwa. Użytkownicy i urządzenia są dodawane lub usuwane, jeśli spełniają warunki dla grupy. Grupy zabezpieczeń mogą być używane dla urządzeń lub użytkowników, ale grupy usługi Office 365 mogą być tylko grupami użytkowników.

## <a name="rule-builder-in-the-azure-portal"></a>Konstruktor reguł w witrynie Azure portal

Usługa Azure AD udostępnia konstruktora reguł, aby szybciej tworzyć i aktualizować ważne reguły. Konstruktor reguł obsługuje konstrukcję do pięciu wyrażeń. Konstruktor reguł ułatwia tworzenie reguły za pomocą kilku prostych wyrażeń, jednak nie można jej odtworzyć do odtworzenia każdej reguły. Jeśli konstruktor reguł nie obsługuje reguły, którą chcesz utworzyć, możesz użyć tego pola tekstowego.

Oto kilka przykładów zaawansowanych reguł lub składni, dla których zaleca się konstruowanie przy użyciu pola tekstowego:

- Reguła z więcej niż pięcioma wyrażeniami
- Reguła Raporty bezpośrednie
- Ustawianie [pierwszeństwa operatora](groups-dynamic-membership.md#operator-precedence)
- [Reguły ze złożonymi wyrażeniami;](groups-dynamic-membership.md#rules-with-complex-expressions) na przykład`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Konstruktor reguł może nie być w stanie wyświetlić niektórych reguł skonstruowanych w polu tekstowym. Może zostać wyświetlony komunikat, gdy konstruktor reguł nie jest w stanie wyświetlić reguły. Konstruktor reguł nie zmienia w żaden sposób obsługiwanej składni, sprawdzania poprawności ani przetwarzania reguł grupy dynamicznej.

![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-create-rule/update-dynamic-group-rule.png)

Aby zapoznać się z przykładami składni, obsługiwanych właściwości, operatorów i wartości reguły członkostwa, zobacz [Dynamiczne reguły członkostwa dla grup w usłudze Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Aby utworzyć regułę członkostwa w grupie

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które znajduje się w roli administratora usługi Global, Administrator usługi Intune lub Administrator użytkownika w dzierżawie.
1. Wyszukaj i wybierz pozycję **Grupy**.
1. Wybierz **pozycję Wszystkie grupy**i wybierz pozycję Nowa **grupa**.

   ![Wybierz polecenie, aby dodać nową grupę](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Na stronie **Grupa** wprowadź nazwę i opis nowej grupy. Wybierz **typ członkostwa** dla użytkowników lub urządzeń, a następnie wybierz pozycję **Dodaj kwerendę dynamiczną**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać więcej niż pięć wyrażeń, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla kwerendy członkowskiej:
   1. Wybierz **pobierz niestandardowe właściwości rozszerzenia**
   1. Wprowadź identyfikator aplikacji, a następnie wybierz polecenie **Odśwież właściwości**.
1. Po utworzeniu reguły wybierz pozycję **Zapisz**.
1. Wybierz **pozycję Utwórz** na stronie **Nowa grupa,** aby utworzyć grupę.

Jeśli wprowadzona reguła jest nieprawidłowa, wyjaśnienie, dlaczego reguła nie może zostać przetworzona, jest wyświetlane w powiadomieniu platformy Azure w portalu. Przeczytaj go uważnie, aby zrozumieć, jak naprawić regułę.

## <a name="to-update-an-existing-rule"></a>Aby zaktualizować istniejącą regułę

1. Zaloguj się do [centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy za pomocą konta, które znajduje się w roli administratora globalnego, administratora grupy, usługi Intune lub administratora użytkownika w dzierżawie.
1. Wybierz **pozycję Grupy** > **Wszystkie grupy**.
1. Wybierz grupę, aby otworzyć jej profil.
1. Na stronie profilu grupy wybierz pozycję **Dynamiczne reguły członkostwa**. Konstruktor reguł obsługuje maksymalnie pięć wyrażeń. Aby dodać więcej niż pięć wyrażeń, należy użyć pola tekstowego.

   ![Dodawanie reguły członkostwa dla grupy dynamicznej](./media/groups-create-rule/update-dynamic-group-rule.png)

1. Aby wyświetlić niestandardowe właściwości rozszerzenia dostępne dla reguły członkostwa:
   1. Wybierz **pobierz niestandardowe właściwości rozszerzenia**
   1. Wprowadź identyfikator aplikacji, a następnie wybierz polecenie **Odśwież właściwości**.
1. Po zaktualizowaniu reguły wybierz pozycję **Zapisz**.

## <a name="turn-on-or-off-welcome-email"></a>Włączanie lub wyłączanie powitalnej poczty e-mail

Po utworzeniu nowej grupy usługi Office 365 wysyłane są powitalne powiadomienie e-mail z użytkownikami dodawanymi do grupy. Później, jeśli zmienią się atrybuty użytkownika lub urządzenia, wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem zmian członkostwa. Użytkownicy, którzy zostaną dodana, otrzymają również powiadomienie powitalne. To zachowanie można wyłączyć w [programie Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Sprawdzanie stanu przetwarzania dla reguły

Stan przetwarzania członkostwa i ostatnia zaktualizowana data można zobaczyć na stronie **Przegląd** grupy.
  
  ![wyświetlanie stanu grupy dynamicznej](./media/groups-create-rule/group-status.png)

Następujące komunikaty o stanie mogą być wyświetlane dla **statusu przetwarzania członkostwa:**

- **Ocena:** Zmiana grupy została odebrana, a aktualizacje są oceniane.
- **Przetwarzanie:** Aktualizacje są przetwarzane.
- **Aktualizacja zakończona**: Przetwarzanie zostało zakończone i wszystkie odpowiednie aktualizacje zostały wykonane.
- **Błąd przetwarzania:** Nie można ukończyć przetwarzania z powodu błędu oceniającego regułę członkostwa.
- **Wstrzymana aktualizacja**: Dynamiczne aktualizacje reguł członkostwa zostały wstrzymane przez administratora. MembershipRuleProcessingState jest ustawiony na "Wstrzymane".

Następujące komunikaty o stanie mogą być wyświetlane dla **ostatniego stanu członkostwa zaktualizowanego:**

- &lt;**Data i godzina:**&gt;ostatni raz członkostwo zostało zaktualizowane.
- **W toku:** Aktualizacje są obecnie w toku.
- **Nieznany:** Nie można pobrać czasu ostatniej aktualizacji. Grupa może być nowa.

Jeśli podczas przetwarzania reguły członkostwa dla określonej grupy wystąpi błąd, w górnej części **strony Przegląd** grupy zostanie wyświetlony alert. Jeśli nie można przetwarzać żadnych oczekujących aktualizacji członkostwa dynamicznego dla wszystkich grup w dzierżawie przez ponad 24 godziny, na górze **wszystkich grup**wyświetlany jest alert .

![przetwarzanie alertów o błędach](./media/groups-create-rule/processing-error.png)

Te artykuły zawierają dodatkowe informacje na temat grup w usłudze Azure Active Directory.

- [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
