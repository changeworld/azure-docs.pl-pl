---
title: Jak utworzyć żądanie pomocy technicznej platformy Azure | Dokumenty firmy Microsoft
description: Klienci, którzy potrzebują pomocy, mogą korzystać z witryny Azure portal, aby znaleźć rozwiązania samoobsługowe oraz tworzyć żądania pomocy technicznej i zarządzać nimi.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 2b68b6ddf19d6b07475e7009b47e162bfb2d0d2f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478988"
---
# <a name="how-to-create-an-azure-support-request"></a>Jak utworzyć żądanie pomocy technicznej dla platformy Azure

## <a name="overview"></a>Omówienie

Platforma Azure umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi, nazywane również biletami pomocy technicznej. Można tworzyć żądania i zarządzać nimi w [witrynie Azure portal](https://portal.azure.com), która jest omówiona w tym artykule. Można również tworzyć żądania i zarządzać nimi programowo, korzystając z [interfejsu API REST dla klientów pomocy technicznej platformy Azure](/rest/api/support).

> [!NOTE]
> Adres URL portalu Azure jest specyficzny dla chmury platformy Azure, w której jest wdrażana twoja organizacja.
>
>* Portal Azure do użytku komercyjnego to:[https://portal.azure.com](https://portal.azure.com)
>* Portal Azure dla Niemiec to:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portal Azure dla rządu Stanów Zjednoczonych to:[https://portal.azure.us](https://portal.azure.us)
>
>

Na podstawie opinii klientów zaktualizowaliśmy środowisko żądania pomocy technicznej, aby skupić się na trzech głównych celach:

* **Usprawnione**: Ułatwiaj znajdowanie i uproszczenie sposobu przesyłania żądania pomocy technicznej i rozwiązywania problemów.
* **Zintegrowane:** Możesz łatwo otworzyć żądanie pomocy technicznej podczas rozwiązywania problemu z zasobem platformy Azure bez przełączania kontekstu.
* **Wydajne:** Zbierz kluczowe informacje potrzebne agentowi pomocy technicznej, aby skutecznie rozwiązać problem.

## <a name="getting-started"></a>Wprowadzenie

Możesz uzyskać **pomoc + pomoc techniczną** w witrynie Azure portal. Jest on dostępny z menu portalu Azure, nagłówka globalnego lub menu zasobów dla usługi. Aby można było złożyć żądanie pomocy technicznej, musisz mieć odpowiednie uprawnienia.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Aby utworzyć żądanie pomocy technicznej, musisz być [właścicielem,](../../role-based-access-control/built-in-roles.md#owner) [współautorem](../../role-based-access-control/built-in-roles.md#contributor) lub być przypisany do roli [współautora żądania pomocy technicznej](../../role-based-access-control/built-in-roles.md#support-request-contributor) na poziomie subskrypcji. Aby utworzyć żądanie pomocy technicznej bez subskrypcji, na przykład scenariusz usługi Azure Active Directory (AAD), musisz być [administratorem](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Przejdź do pomocy + wsparcie z nagłówka globalnego

Aby uruchomić żądanie pomocy technicznej z dowolnego miejsca w witrynie Azure portal:

1. Wybierz ikonę **?** w nagłówku globalnym. Następnie wybierz **pomoc + wsparcie**.

   ![Pomoc i obsługa techniczna](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Wybierz **pozycję Nowe żądanie pomocy technicznej**. Postępuj zgodnie z instrukcjami, aby przekazać nam informacje o twoim problemie. Zaproponujemy kilka możliwych rozwiązań, zbierzemy szczegółowe informacje na temat problemu oraz pomożemy Ci przesłać i śledzić prośbę o pomoc techniczną.

   ![Nowe żądanie pomocy technicznej](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Przejdź do pomocy + wsparcie z menu zasobów

Aby uruchomić żądanie pomocy technicznej w kontekście zasobu, obecnie pracujesz z:

1. Z menu zasobów w sekcji **Pomoc techniczna + Rozwiązywanie problemów** wybierz pozycję **Nowe żądanie pomocy technicznej**.

   ![W kontekście](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Postępuj zgodnie z instrukcjami, aby przekazać nam informacje o problemie, który masz. Po uruchomieniu procesu żądania pomocy technicznej z zasobu niektóre opcje są wstępnie wybrane dla Ciebie.

## <a name="create-a-support-request"></a>Tworzenie żądania obsługi

Przejdziemy przez kilka kroków, aby zebrać informacje o twoim problemie i pomóc ci go rozwiązać. Każdy krok jest opisany w poniższych sekcjach.

### <a name="basics"></a>Podstawy

Pierwszy krok procesu żądania pomocy technicznej zbiera podstawowe informacje o problemie i planie pomocy technicznej.

Na karcie **Podstawy** **żądania pomocy technicznej użyj**selektorów, aby zacząć informować nas o problemie. Najpierw zidentyfikujesz niektóre ogólne kategorie dla typu problemu i wybierz powiązaną subskrypcję. Wybierz usługę, na przykład **maszynę wirtualną z systemem Windows**. Wybierz zasób, na przykład nazwę maszyny wirtualnej. Opisz problem własnymi słowami, a następnie **wybierz typ problemu,** aby uzyskać bardziej szczegółowe.

![Blok Podstawowe](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> Platforma Azure zapewnia nieograniczoną obsługę zarządzania subskrypcjami, która obejmuje rozliczenia, korekty przydziałów i transfery kont. Aby uzyskać pomoc techniczną, potrzebujesz planu pomocy technicznej. [Dowiedz się więcej o planach pomocy technicznej](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Rozwiązania

Po zebraniu podstawowych informacji, następnie pokażemy Ci rozwiązania, które możesz wypróbować na własną rękę. W niektórych przypadkach możemy nawet uruchomić szybką diagnostykę. Rozwiązania są pisane przez inżynierów platformy Azure i rozwiążą najczęstsze problemy.

### <a name="details"></a>Szczegóły

Następnie zbieramy dodatkowe szczegóły dotyczące problemu. Podanie dokładnych i szczegółowych informacji w tym kroku pomaga nam skierować prośbę o pomoc techniczną do odpowiedniego agenta.

Jeśli to możliwe, powiedz nam, kiedy problem się rozpoczął i wszelkie kroki, aby go odtworzyć. Można przekazać plik, taki jak plik dziennika lub dane wyjściowe z diagnostyki.

Po tym, jak mamy wszystkie informacje o problemie, wybierz, jak uzyskać wsparcie. W sekcji **Metoda pomocy technicznej** w sekcji **Szczegóły**wybierz ważność uderzenia. Podaj preferowaną metodę kontaktu, dobry czas na skontaktowanie się z Tobą i język pomocy technicznej.

Następnie wypełnij sekcję **Informacje kontaktowe,** abyśmy wiedzieli, jak się z Tobą skontaktować.

### <a name="review--create"></a>Recenzja + tworzenie

Wypełnij wszystkie wymagane informacje na każdej karcie, a następnie wybierz **pozycję Przejrzyj + utwórz**. Sprawdź szczegóły, które wyślesz do pomocy technicznej. Wróć do dowolnej karty, aby w razie potrzeby wprowadzić zmiany. Po spełnieniu prośby o pomoc techniczną wybierz pozycję **Utwórz**.

Agent pomocy technicznej skontaktuje się z Tobą przy użyciu wskazanej metody. Aby uzyskać informacje na temat początkowego czasu reakcji, zobacz [Zakres pomocy technicznej i czas reakcji](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Wszystkie żądania pomocy technicznej

Możesz wyświetlić szczegóły i stan żądań pomocy technicznej, przechodząc do **Pomocy + obsługa** >  **wszystkich żądań pomocy technicznej**.

![Wszystkie żądania pomocy technicznej](./media/how-to-create-azure-support-request/allrequestslower.png)

Na tej stronie można filtrować żądania pomocy technicznej według **subskrypcji,** daty **utworzenia** (UTC) i **stanu.** Ponadto można sortować i wyszukiwać żądania pomocy technicznej na tej stronie.

Wybierz żądanie pomocy technicznej, aby wyświetlić szczegóły, w tym ważność i oczekiwany czas, jaki zajmie agentowi pomocy technicznej udzielenie odpowiedzi.

Jeśli chcesz zmienić ważność żądania, wybierz opcję **Wpływ na działalność**. Wybierz z listy ważności do przypisania.

> [!NOTE]
> Maksymalny poziom ważności zależy od planu pomocy technicznej. [Dowiedz się więcej o planach pomocy technicznej](https://azure.microsoft.com/support/plans).
>
>
Aby dowiedzieć się więcej o opcjach samodzielnej pomocy technicznej na platformie Azure, obejrzyj ten klip wideo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Następne kroki

* [Wyślij nam swoją opinię i sugestie](https://feedback.azure.com/forums/266794-support-feedback)
* Zaangażuj się z nami na [Twitterze](https://twitter.com/azuresupport)
* Uzyskaj pomoc od swoich rówieśników na [forach MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Dowiedz się więcej w [często zadawanych pytaniach z pomocy technicznej platformy Azure](https://azure.microsoft.com/support/faq)
