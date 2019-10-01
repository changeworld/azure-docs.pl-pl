---
title: Przypisywanie dostępu do danych Azure Cost Management | Microsoft Docs
description: W tym artykule omówiono przypisanie uprawnień do Azure Cost Management danych dla różnych zakresów dostępu.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: f9db07f648956130bb5bdebb23321b0eb14679c7
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695398"
---
# <a name="assign-access-to-cost-management-data"></a>Przypisywanie dostępu do danych Cost Management

W przypadku użytkowników korzystających z umów Enterprise Agreement w ramach usługi Azure Portal i portalu Enterprise (EA) można zdefiniować poziom dostępu do Azure Cost Management danych. W przypadku użytkowników z innymi typami kont platformy Azure Definiowanie poziomu dostępu do danych Cost Management jest prostsze przy użyciu kontroli dostępu opartej na rolach na platformie Azure. W tym artykule przedstawiono sposób przypisywania dostępu do danych Cost Management. Po przypisaniu uprawnień użytkownik przegląda dane w Cost Management na podstawie zakresu, do którego mają dostęp, i w zakresie wybranych w Azure Portal.

Zakres, który wybiera użytkownik, jest używany w całym Cost Management, aby zapewnić konsolidację danych i kontrolować dostęp do informacji o kosztach. W przypadku korzystania z zakresów użytkownicy nie będą zaznaczać ich wiele. Zamiast tego wybierają większy zakres, do którego są rzutowane zakresy podrzędne, a następnie filtrują się do elementów, które mają być wyświetlane. Należy zrozumieć, że konsolidacja danych jest ważna, ponieważ niektóre osoby nie powinny mieć dostępu do zakresu nadrzędnego, do którego są rzutowane zakresy podrzędne.

Obejrzyj informacje o [sposobie przypisywania dostępu Azure Cost Management](https://www.youtube.com/watch?v=J997ckmwTa8) wideo, aby dowiedzieć się więcej o przypisaniu dostępu do wyświetlania kosztów i opłat za pomocą kontroli dostępu opartej na rolach na platformie Azure.

>[!VIDEO https://www.youtube.com/embed/J997ckmwTa8]

## <a name="cost-management-scopes"></a>Zakresy Cost Management

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

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Zapewnianie dostępu do kosztów w portalu EA

Zakres działu wymaga **włączonej** opcji " **da** " w portalu EA. Wszystkie pozostałe zakresy wymagają **włączenia** opcji **Ao Wyświetl opłaty** w portalu EA.

Aby włączyć opcję:

1. Zaloguj się do portalu EA pod adresem [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. W przypadku zakresów usługi Cost Management, do których chcesz zapewnić dostęp, Włącz opcję opłata, aby **wyświetlić opłaty za usługę da** i/lub **Ao**.  
    Karta @no__t 0Enrollment z opcjami opcji DA i AO Wyświetl opłaty @ no__t-1

Po włączeniu opcji wyświetlania opłaty większość zakresów wymaga również konfiguracji uprawnień kontroli dostępu opartej na rolach (RBAC) w Azure Portal.

## <a name="enterprise-administrator-role"></a>Rola administratora przedsiębiorstwa

Domyślnie administrator przedsiębiorstwa ma dostęp do konta rozliczeniowego (Umowa Enterprise/rejestracji) i wszystkich innych zakresów, które są zakresami podrzędnymi. Administrator przedsiębiorstwa przypisuje dostęp do zakresów innym użytkownikom. Najlepszym rozwiązaniem w zakresie ciągłości biznesowej jest zawsze posiadanie dwóch użytkowników z dostępem administratora przedsiębiorstwa. Poniższe sekcje zawierają instrukcje dla administratorów przedsiębiorstwa przypisujące dostęp do zakresów innym użytkownikom.

## <a name="assign-billing-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rozliczeń

Dostęp do zakresu konta rozliczeniowego wymaga uprawnień administratora przedsiębiorstwa w portalu EA. Administrator przedsiębiorstwa ma dostęp do wyświetlania kosztów całej rejestracji w ramach umowy EA lub rejestracji wielu. W Azure Portal dla zakresu konta rozliczeń nie jest wymagana żadna akcja.

1. Zaloguj się do portalu EA pod adresem [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.  
    @no__t — 0select swoją rejestrację w witrynie EA Portal @ no__t-1
4. Kliknij pozycję **+ Dodaj administratora**.
5. W polu Dodaj administratora wybierz typ uwierzytelniania i wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **tylko do odczytu**wybierz pozycję **tak**.  W przeciwnym razie wybierz pozycję **nie**.
7. Kliknij przycisk **Dodaj** , aby utworzyć konto.  
    ![example informacje wyświetlane w oknie Dodawanie administratora @ no__t-1

Może upłynąć do 30 minut, zanim nowy użytkownik będzie mógł uzyskać dostęp do danych w Cost Management.

### <a name="assign-department-scope-access"></a>Przypisywanie dostępu do zakresu działu

Dostęp do zakresu działu wymaga dostępu administratora działu (opłata za usługę DA Podgląd) w portalu EA. Administrator działu ma dostęp do wyświetlania kosztów i danych użycia skojarzonych z działem lub do wielu działów. Dane działu obejmują wszystkie subskrypcje należące do konta rejestracji, które są połączone z działem. W Azure Portal nie jest wymagana żadna akcja.

1. Zaloguj się do portalu EA pod adresem [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.
4. Kliknij kartę **dział** , a następnie kliknij pozycję **Dodaj administratora**.
5. W oknie Dodaj administratora działu wybierz typ uwierzytelniania, a następnie wpisz adres e-mail użytkownika.
6. Jeśli użytkownik powinien mieć dostęp tylko do odczytu do danych o kosztach i użyciu, w obszarze **tylko do odczytu**wybierz pozycję **tak**.  W przeciwnym razie wybierz pozycję **nie**.
7. Wybierz działy, do których chcesz przyznać uprawnienia administracyjne działu.
8. Kliknij przycisk **Dodaj** , aby utworzyć konto.  
    ![enter wymagane informacje w oknie Dodaj administratora działu @ no__t-1

## <a name="assign-enrollment-account-scope-access"></a>Przypisywanie dostępu do zakresu konta rejestracji

Dostęp do zakresu konta rejestracji wymaga dostępu właściciela konta (AO View) w portalu EA. Właściciel konta może wyświetlać koszty i dane użycia skojarzone z subskrypcjami utworzonymi na podstawie tego konta rejestracji. W Azure Portal nie jest wymagana żadna akcja.

1. Zaloguj się do portalu EA pod adresem [https://ea.azure.com](https://ea.azure.com) przy użyciu konta administratora przedsiębiorstwa.
2. W lewym okienku wybierz pozycję **Zarządzaj** .
3. Na karcie **rejestracja** wybierz rejestrację, którą chcesz zarządzać.
4. Kliknij kartę **konto** , a następnie kliknij pozycję **Dodaj konto**.
5. W polu Dodaj konto wybierz **dział** , do którego chcesz skojarzyć konto, lub pozostaw je jako nieprzypisane.
6. Wybierz typ uwierzytelniania i wpisz nazwę konta.
7. Wpisz adres e-mail użytkownika, a następnie opcjonalnie wpisz centrum kosztu.
8. Kliknij pozycję **Dodaj** , aby utworzyć konto.  
    ![enter wymagane informacje w polu Dodaj konto dla konta rejestracji @ no__t-1

Po wykonaniu powyższych czynności konto użytkownika stanie się kontem rejestracji w witrynie Enterprise Portal i może tworzyć subskrypcje. Użytkownik może uzyskać dostęp do danych o kosztach i użyciu dla utworzonych przez siebie subskrypcji.

## <a name="assign-management-group-scope-access"></a>Przypisywanie dostępu do zakresu grupy zarządzania

Dostęp do widoku zakres grupy zarządzania wymaga co najmniej uprawnienia czytelnik Cost Management (lub czytelnik). W Azure Portal można skonfigurować uprawnienia dla grupy zarządzania. Musisz mieć co najmniej uprawnienia administratora dostępu użytkownika (lub właściciela) dla grupy zarządzania, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **wszystkie usługi** , Wyszukaj pozycję _grupy zarządzania_, a następnie wybierz pozycję **grupy zarządzania**.
3. Wybierz grupę zarządzania w hierarchii.
4. Obok nazwy grupy zarządzania kliknij pozycję **szczegóły**.
5. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
6. Kliknij pozycję **Add** (Dodaj).
7. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
8. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
9. Aby przypisać dostęp, Wyszukaj, a następnie wybierz użytkownika.
10. Kliknij przycisk **Save** (Zapisz).  
    ![example informacje w polu Dodaj uprawnienia dla grupy zarządzania @ no__t-1

## <a name="assign-subscription-scope-access"></a>Przypisywanie dostępu do zakresu subskrypcji

Dostęp do subskrypcji wymaga co najmniej uprawnienia czytnika Cost Management (lub czytelnika). Uprawnienia do subskrypcji można skonfigurować w Azure Portal. Musisz mieć co najmniej uprawnienia administratora dostępu użytkownika (lub właściciela) dla subskrypcji, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi** na pasku bocznym, Wyszukaj pozycję _subskrypcje_, a następnie wybierz pozycję **subskrypcje**.
3. Wybierz subskrypcję.
4. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
7. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
8. Aby przypisać dostęp, Wyszukaj, a następnie wybierz użytkownika.
9. Kliknij przycisk **Save** (Zapisz).

## <a name="assign-resource-group-scope-access"></a>Przypisywanie dostępu do zakresu grupy zasobów

Dostęp do grupy zasobów wymaga co najmniej uprawnienia czytnika Cost Management (lub czytelnika). Można skonfigurować uprawnienia do grupy zasobów w Azure Portal. Musisz mieć co najmniej uprawnienia administratora dostępu użytkownika (lub właściciela) dla grupy zasobów, aby umożliwić dostęp innym osobom. W przypadku kont EA platformy Azure należy również włączyć ustawienie **Ao Wyświetl opłaty** w portalu EA.

1. Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).
2. Na pasku bocznym wybierz pozycję **wszystkie usługi** , Wyszukaj pozycję _grupy zasobów_, a następnie wybierz pozycję **grupy zasobów**.
3. Wybierz grupę zasobów.
4. Wybierz pozycję **Access Control (IAM)** w okienku po lewej stronie.
5. Kliknij pozycję **Add** (Dodaj).
6. W obszarze **rola**wybierz pozycję **Cost Management Reader**.
7. W obszarze **Przypisywanie dostępu do**wybierz pozycję **użytkownik, Grupa lub aplikacja usługi Azure AD**.
8. Aby przypisać dostęp, Wyszukaj, a następnie wybierz użytkownika.
9. Kliknij przycisk **Save** (Zapisz).

## <a name="cross-tenant-authentication-issues"></a>Problemy z uwierzytelnianiem między dzierżawcami

Obecnie Azure Cost Management ma ograniczoną obsługę uwierzytelniania między dzierżawcami. W niektórych sytuacjach podczas próby uwierzytelnienia w dzierżawach może zostać wyświetlony komunikat o błędzie **odmowy dostępu** w analizie kosztów. Ten problem może wystąpić, jeśli skonfigurujesz kontrolę dostępu opartą na rolach (RBAC) w ramach subskrypcji innej dzierżawy, a następnie spróbujesz wyświetlić dane dotyczące kosztów.

*Aby obejść ten problem*: po SKONFIGUROWANiu RBAC międzydzierżawci odczekaj godzinę. Następnie spróbuj wyświetlić koszty w analizie kosztów lub przyznać Cost Management dostęp do użytkowników w obu dzierżawcach.  


## <a name="next-steps"></a>Następne kroki

- Jeśli nie zostało to jeszcze zrobione z pierwszego przewodnika Szybki Start dla Cost Management, zapoznaj się z tematem [Rozpocznij analizowanie kosztów](quick-acm-cost-analysis.md).
