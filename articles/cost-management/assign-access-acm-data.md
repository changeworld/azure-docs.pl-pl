---
title: Przypisywanie dostępu do danych Azure Cost Management
description: W tym artykule opisano jednak przypisywanie uprawnień do danych w usłudze Azure Cost Management różne zakresy dostępu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: e3140ee990127db6815828314103a09dff7cf26e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74219846"
---
# <a name="assign-access-to-cost-management-data"></a>Przypisywanie dostępu do danych rozwiązania Cost Management

W przypadku użytkowników korzystających z umów Enterprise Agreement w ramach usługi Azure Portal i portalu Enterprise (EA) można zdefiniować poziom dostępu do Azure Cost Management danych. W przypadku użytkowników z innymi typami kont platformy Azure Definiowanie poziomu dostępu do danych Cost Management jest prostsze przy użyciu kontroli dostępu opartej na rolach na platformie Azure. Ten artykuł przeprowadzi przypisywanie dostępu do danych rozwiązania Cost Management. Po przypisaniu kombinację uprawnień dane użytkownika widoków w Cost Management na podstawie zakresu dostępu i na zakres, wybierają w witrynie Azure portal.

Zakres, który użytkownik wybierze jest używana w całej Cost Management, zapewnienie konsolidacji danych i kontrolować dostęp do informacji o kosztach. Podczas korzystania z zakresów, użytkownicy nie zaznacz je. Zamiast tego wybierz większy zakres, który zakresy podrzędne Przywracanie do, a następnie ich filtru w celu mają być wyświetlane. Konsolidacja danych ważne jest, aby zrozumieć, ponieważ niektórzy użytkownicy nie powinien mieć dostęp do zakresu nadrzędnego, który zakresy podrzędne Przywracanie do.

Obejrzyj informacje o [sposobie przypisywania dostępu Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) wideo, aby dowiedzieć się więcej o przypisaniu dostępu do wyświetlania kosztów i opłat za pomocą kontroli dostępu opartej na rolach na platformie Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Usługa Cost Management zakresów

Zarządzanie kosztami obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Typ konta określa dostępne zakresy.

### <a name="azure-ea-subscription-scopes"></a>Zakresy subskrypcji platformy Azure EA

Aby wyświetlić dane dotyczące kosztów dla subskrypcji EA platformy Azure, użytkownik musi mieć co najmniej jeden dostęp do odczytu do co najmniej jednego z następujących zakresów.

| **Zakres** | **Lokalizacja definicji** | **Wymagany dostęp do wyświetlania danych** | **Wstępnie wymagane ustawienie umowy EA** | **Konsolidowanie danych do** |
| --- | --- | --- | --- | --- |
| Konto rozliczeniowe<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Administrator przedsiębiorstwa | Brak | Wszystkie subskrypcje z umowy Enterprise Agreement |
| Dział | [https://ea.azure.com](https://ea.azure.com/) | Administrator działu | Włączono **opłaty za Podgląd da** | Wszystkie subskrypcje należące do konta rejestracji połączonego z działem |
| Konto rejestracji<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Właściciel konta | Włączono **opłaty za wyświetlanie Ao** | Wszystkie subskrypcje z konta rejestracji |
| Grupa zarządzania | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączono **opłaty za wyświetlanie Ao** | Wszystkie subskrypcje poniżej grupy zarządzania |
| Subskrypcja | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączono **opłaty za wyświetlanie Ao** | Wszystkie zasoby lub grupy zasobów w subskrypcji |
| Grupa zasobów | [https://portal.azure.com](https://portal.azure.com/) | Czytelnik usługi Cost Management (lub Czytelnik) | Włączono **opłaty za wyświetlanie Ao** | Wszystkie zasoby w grupie zasobów |

<sup>1</sup> konto rozliczeniowe jest również określane jako Umowa Enterprise lub Rejestracja.

<sup>2</sup> konto rejestracji jest również określane jako właściciel konta.

Na poniższym diagramie przedstawiono relację między zakresami Cost Management przy użyciu ról i ustawień portalu EA.

![Diagram przedstawiający relację między zakresami Cost Management przy użyciu ról i ustawień portalu EA](./media/assign-access-acm-data/scope-access-relationship-diagram.png)

Gdy w portalu EA są wyłączone **opłaty za usługę DirectAccess** , zobaczysz komunikat z informacją *o kosztach wyłączonych dla Twojej organizacji* , gdy spróbujesz wyświetlić koszty dla działów i kont.

Podobnie, gdy w portalu EA są wyłączone **opłaty za wyświetlanie Ao** , zobaczysz komunikat informujący o *kosztach wyłączonych dla Twojej organizacji* podczas próby wyświetlenia kosztów kont rejestracji, grup zarządzania, subskrypcji i grup zasobów.

## <a name="other-azure-account-scopes"></a>Inne zakresy kont platformy Azure

Aby wyświetlić dane dotyczące kosztów dla innych subskrypcji platformy Azure, użytkownik musi mieć co najmniej jeden dostęp do odczytu do jednego lub kilku z następujących zakresów:

- Konto platformy Azure
- Grupa zarządzania
- Grupa zasobów

Różne zakresy są dostępne po dołączeniu klientów do umowy klienta firmy Microsoft przez partnerów. Klienci korzystający z programu CSP mogą następnie korzystać z funkcji Cost Management po włączeniu ich przez partnera CSP. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do Azure Cost Management dla partnerów](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Zapewnianie dostępu do kosztów w witrynie EA portal

Zakres działu wymaga **włączonej** opcji " **da** " w portalu EA. Wszystkie pozostałe zakresy wymagają **włączenia** opcji **Ao Wyświetl opłaty** w portalu EA.

Aby włączyć opcję:

1. Zaloguj się do portalu EA w [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. W przypadku zakresów usługi Cost Management, do których chcesz zapewnić dostęp, Włącz opcję opłata, aby **wyświetlić opłaty za usługę da** i/lub **Ao**.  
    Karta Rejestracja ![wyświetlana w opcjach opcji DA i AO Wyświetl opłaty](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Po włączeniu opcji opłat widoku zakresów większość również wymagać dostępu opartej na rolach kontroli uprawnień konfiguracji w witrynie Azure portal.

## <a name="enterprise-administrator-role"></a>Rola administratora przedsiębiorstwa

Domyślnie administrator przedsiębiorstwa ma dostęp do konta rozliczeniowego (Enterprise Agreement/rejestracji) i wszystkie inne zakresy, które są zakresy podrzędne. Administrator przedsiębiorstwa przydziela dostęp do zakresów dla innych użytkowników. Najlepszym rozwiązaniem dla ciągłości działania powinny zawsze występować dwa użytkownikom dostępu administratora przedsiębiorstwa. Poniższe sekcje zawierają przykłady przewodniku dostępu przypisywanie administratora przedsiębiorstwa do zakresów dla innych użytkowników.

## <a name="assign-billing-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rozliczeniowego

Dostęp do rozliczeń zakresu konto wymaga uprawnień administratora przedsiębiorstwa w witrynie EA portal. Administrator przedsiębiorstwa ma dostęp do wyświetlania kosztów całej rejestracji EA lub wiele rejestracji. W witrynie Azure portal rozliczeń zakresu konta są wymagane żadne działania.

1. Zaloguj się do portalu EA w [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.  
    ![wybrać swoją rejestrację w portalu EA](./media/assign-access-acm-data/ea-portal.png)
4. Kliknij pozycję **+ Dodaj administratora**.
5. W oknie dialogowym Dodawanie administratora wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **tylko do odczytu**wybierz pozycję **tak**.  W przeciwnym razie wybierz pozycję **nie**.
7. Kliknij przycisk **Dodaj** , aby utworzyć konto.  
    ![przykładowe informacje wyświetlane w polu Dodaj administratora](./media/assign-access-acm-data/add-admin.png)

Może potrwać do 30 minut, zanim nowy użytkownik mają dostęp do danych w Cost Management.

### <a name="assign-department-scope-access"></a>Przypisywanie działu zakresu dostępu

Dostęp do zakresu działu wymaga dostępu (DA Wyświetl opłaty) administratora działu w witrynie EA portal. Administrator działu ma dostęp do wyświetlenia kosztów i użycia danych skojarzonych z działu lub wielu działów. Dane dla działu obejmują wszystkie subskrypcje należące do konta rejestracji, które są połączone z działu. W witrynie Azure portal są wymagane żadne działania.

1. Zaloguj się do portalu EA w [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.
4. Kliknij kartę **dział** , a następnie kliknij pozycję **Dodaj administratora**.
5. W oknie dialogowym Dodawanie administratora działu wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **tylko do odczytu**wybierz pozycję **tak**.  W przeciwnym razie wybierz pozycję **nie**.
7. Wybierz działów, które chcesz udzielić uprawnień administracyjnych działu do.
8. Kliknij przycisk **Dodaj** , aby utworzyć konto.  
    ![wprowadź wymagane informacje w oknie Dodaj administratora działu](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Przypisz dostęp do zakresu konta rejestracji

Dostęp do zakresu konta rejestracji wymaga dostępu (AO Wyświetl opłaty) do właściciela konta w witrynie EA portal. Właściciel konta mogą wyświetlać, kosztów i użycia danych skojarzonymi z subskrypcjami, utworzone na podstawie tego konta rejestracji. W witrynie Azure portal są wymagane żadne działania.

1. Zaloguj się do portalu EA w [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.
4. Kliknij kartę **konto** , a następnie kliknij pozycję **Dodaj konto**.
5. W polu Dodaj konto wybierz **dział** , do którego chcesz skojarzyć konto, lub pozostaw je jako nieprzypisane.
6. Wybierz typ uwierzytelniania, a następnie wpisz nazwę konta.
7. Wpisz adres e-mail użytkownika, a następnie opcjonalnie wpisz centrum kosztów.
8. Kliknij pozycję **Dodaj** , aby utworzyć konto.  
    ![wprowadź wymagane informacje w polu Dodaj konto dla konta rejestracji](./media/assign-access-acm-data/add-account.png)

Po wykonaniu powyższych kroków, konto użytkownika, staje się kontem rejestracji w witrynie Enterprise portal i można tworzyć subskrypcje. Użytkownik może uzyskać dostęp do kosztów i użycia danych w przypadku subskrypcji, które tworzą.

## <a name="assign-management-group-scope-access"></a>Przypisz dostęp do zakresu grupy zarządzania

Dostęp do widoku zakres grupy zarządzania wymaga co najmniej uprawnienia czytelnik Cost Management (lub czytelnik). Można skonfigurować uprawnienia dla grupy zarządzania, w witrynie Azure portal. Masz co najmniej uprawnienia administratora dostępu użytkowników (lub właściciela) dla grupy zarządzania włączyć dostęp do innych użytkowników. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **wszystkie usługi** , Wyszukaj pozycję _grupy zarządzania_, a następnie wybierz pozycję **grupy zarządzania**.
3. Wybierz grupy zarządzania w hierarchii.
4. Obok nazwy grupy zarządzania kliknij pozycję **szczegóły**.
5. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
6. Kliknij pozycję **Add** (Dodaj).
7. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
8. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
9. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
10. Kliknij pozycję **Zapisz**.  
    ![przykładowe informacje w polu Dodaj uprawnienia dla grupy zarządzania](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Przypisywanie subskrypcji zakresu dostępu

Dostęp do subskrypcji wymaga co najmniej uprawnienia czytnika zarządzania kosztami (lub czytnika). Można skonfigurować uprawnienia do subskrypcji, w witrynie Azure portal. Masz co najmniej uprawnienia administratora dostępu użytkowników (lub właściciela) dla subskrypcji włączyć dostęp do innych użytkowników. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi** na pasku bocznym, Wyszukaj pozycję _subskrypcje_, a następnie wybierz pozycję **subskrypcje**.
3. Wybierz subskrypcję.
4. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
7. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
8. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
9. Kliknij pozycję **Zapisz**.

## <a name="assign-resource-group-scope-access"></a>Przypisz dostęp do zakresu grupy zasobów

Dostęp do grupy zasobów wymaga co najmniej uprawnienia czytnika zarządzania kosztami (lub czytnika). Można skonfigurować uprawnienia do grupy zasobów, w witrynie Azure portal. Masz co najmniej uprawnienia administratora dostępu użytkowników (lub właściciela) dla grupy zasobów włączyć dostęp do innych użytkowników. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **wszystkie usługi** , Wyszukaj pozycję _grupy zasobów_, a następnie wybierz pozycję **grupy zasobów**.
3. Wybierz grupę zasobów.
4. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
7. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
8. Aby przypisać dostęp, wyszukiwania, a następnie wybierz użytkownika.
9. Kliknij pozycję **Zapisz**.

## <a name="cross-tenant-authentication-issues"></a>Problemy z uwierzytelnianiem między dzierżawcami

Obecnie Azure Cost Management ma ograniczoną obsługę uwierzytelniania między dzierżawcami. W niektórych sytuacjach podczas próby uwierzytelnienia w dzierżawach może zostać wyświetlony komunikat o błędzie **odmowy dostępu** w analizie kosztów. Ten problem może wystąpić, jeśli skonfigurujesz kontrolę dostępu opartą na rolach (RBAC) w ramach subskrypcji innej dzierżawy, a następnie spróbujesz wyświetlić dane dotyczące kosztów.

*Aby obejść ten problem*: po SKONFIGUROWANiu RBAC międzydzierżawci odczekaj godzinę. Następnie spróbuj wyświetlić koszty w analizie kosztów lub przyznać Cost Management dostęp do użytkowników w obu dzierżawcach.  


## <a name="next-steps"></a>Następne kroki

- Jeśli nie zostało to jeszcze zrobione z pierwszego przewodnika Szybki Start dla Cost Management, zapoznaj się z tematem [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md).
