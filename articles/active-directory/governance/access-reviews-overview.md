---
title: Co to są przeglądy dostępu? — Azure Active Directory | Microsoft Docs
description: Korzystanie z przeglądów dostępu w usłudze Azure Active Directory, możesz kontrolować grupy członkostwa i dostęp do aplikacji do spełnienia warunków nadzoru, zarządzania ryzykiem i inicjatyw zgodności w organizacji.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1563a023f397999deb5c6abd40843d6a376b0492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351448"
---
# <a name="what-are-azure-ad-access-reviews"></a>Co to są usługi Azure AD access przeglądy?

Przeglądy dostępu w usłudze Azure Active Directory (Azure AD) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Dostęp użytkownika można przeczytać w regularnych odstępach czasu, aby upewnić się, że odpowiednie osoby mieć przedłużony dostęp.

Oto film wideo, który zawiera krótkie omówienie przeglądów dostępu:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Dlaczego to access przeglądy istotne?

Usługa Azure AD umożliwia współpracę wewnętrznie w Twojej organizacji i użytkowników z organizacji zewnętrznych, takich jak partnerów. Użytkownicy mogą dołączenia do grup, zapraszać gości, nawiązać połączenie z aplikacji w chmurze i pracę zdalną z ich pracy lub urządzeń osobistych. Wygodne korzystające z możliwości samoobsługi spowodowało potrzebę lepsze funkcje zarządzania dostępem.

- Jak dołączyć nowych pracowników, jak zapewnić, że mają prawo dostępu do wydajnej?
- Osoby przenieść zespoły lub odchodzą z firmy, jak zapewnić, że stary dostęp zostanie usunięty, szczególnie w przypadku, gdy obejmuje gości?
- Prawa dostępu nadmierne może prowadzić do inspekcji, wnioski i naruszeń, zgodnie z ich oznaczać to Brak kontrolę dostępu.
- Masz proaktywne angażowanie się z właścicielami zasobu, aby upewnić się, że regularnego przeglądu kto ma dostęp do swoich zasobów.

## <a name="when-to-use-access-reviews"></a>Kiedy należy używać dostępu przeglądy?

- **Zbyt wielu użytkowników pełniących uprzywilejowane role:** To dobry pomysł, aby sprawdzić, ilu użytkowników mają dostęp administracyjny, ile z nich są Administratorzy globalni i jeśli istnieją zaproszenie gości lub partnerów, które nie zostały usunięte po przypisywane do wykonywania zadań administracyjnych. Można ponownie certyfikować użytkownicy przypisania roli w [ról usługi Azure AD](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) takich jak Administratorzy globalni lub [role zasobów platformy Azure](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) takie jak Administrator dostępu użytkowników w [usługi Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) środowiska.
- **Jeśli usługa automation jest praktyce:** Można utworzyć reguły dynamicznego członkostwa w grupach zabezpieczeń lub grupach usługi Office 365, ale co zrobić, jeśli dane Kadrowe nie jest w usłudze Azure AD lub jeśli użytkownicy nadal potrzebują dostępu po opuszczeniu grupy to w opracowywaniu ich wymiany? Następnie można utworzyć przeglądu w tej grupie, aby upewnić się, że tych, którzy nadal muszą mieć dostęp powinni mieć przedłużony dostęp.
- **Gdy grupa jest używana do nowego celu:** Jeśli masz grupę, która ma być synchronizowane z usługą Azure AD lub planowane jest umożliwienie aplikacji Salesforce dla wszystkich użytkowników w grupie zespołu sprzedaży, należałoby poproś właściciela grupy, aby przejrzeć członkostwo w grupie przed grupy używany w co różne ryzyka tości.
- **Dostęp do najważniejszych danych biznesowych:** dla niektórych zasobów może być wymagana poprosić osoby spoza IT regularnie wylogowanie i podać uzasadnienie w Dlaczego potrzebują dostępu na potrzeby inspekcji.
- **Aby zachować listy wyjątków zasad:** W świecie idealne, wszyscy użytkownicy wykonać dostępu zasady, aby zabezpieczyć dostęp do zasobów organizacji. Jednak czasami istnieją przypadków biznesowych, które wymagają wprowadzenia wyjątków. Jako administrator IT może zarządzać to zadanie, uniknąć nadzoru wyjątki od zasad i zapewnić audytorów dowód regularnie weryfikowane tych wyjątków.
- **Poproś właściciele grupy, aby upewnić się, że nadal potrzebują gości w ich grupach:** Dostęp pracowników może zautomatyzować wraz z lokalnie zarządzania tożsamościami i Dostępem, ale nie zaproszeni goście. Jeśli grupy zapewnia gości dostęp do poufnej zawartości firmy, a następnie jego odpowiedzialność właściciela grupy, aby potwierdzić gości nadal masz potrzebą biznesową, aby uzyskać dostęp.
- **Mają przeglądy powtarzanie okresowo:** Możesz skonfigurować cykliczny przeglądy dostępu użytkowników częstotliwością zestaw takich jak co tydzień, co miesiąc, co kwartał i co roku i recenzenci będą powiadamiani na początku każdej recenzji. Recenzenci mogli zatwierdzać lub odrzucać dostęp za pomocą interfejsu przyjazne i za pomocą inteligentne zalecenia.

## <a name="where-do-you-create-reviews"></a>Gdzie można utworzyć przeglądy?

W zależności od tego, co chcesz przejrzeć, utworzy zapoznania się z nimi dostępu w usłudze Azure AD dostęp, przeglądy, aplikacje dla przedsiębiorstw usługi Azure AD (w wersji zapoznawczej) lub Azure AD PIM.

| Prawa dostępu użytkowników | Recenzenci mogą być | Utworzono w Przegląd | Środowisko osoby dokonującej przeglądu |
| --- | --- | --- | --- |
| Członkowie grupy zabezpieczeń</br>Członkowie grupy pakietu Office | Określony osób dokonujących przeglądu</br>Właściciele grupy</br>Zapoznaj się samodzielnie | Przeglądy dostępu w usłudze Azure AD</br>Grupy usługi Azure AD | Panel dostępu |
| Przypisane do połączonej aplikacji | Określony osób dokonujących przeglądu</br>Zapoznaj się samodzielnie | Przeglądy dostępu w usłudze Azure AD</br>Usługa Azure AD aplikacji przedsiębiorstwa (w wersji zapoznawczej) | Panel dostępu |
| Rola usługi Azure AD | Określony osób dokonujących przeglądu</br>Zapoznaj się samodzielnie | Azure AD PIM | Azure Portal |
| Rola zasobów platformy Azure | Określony osób dokonujących przeglądu</br>Zapoznaj się samodzielnie | Azure AD PIM | Azure Portal |

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z przeglądów dostępu, musi mieć jeden z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5 license

Aby uzyskać więcej informacji, zobacz [Instrukcje: Zarejestruj się w usłudze Azure Active Directory — wersja Premium](../fundamentals/active-directory-get-started-premium.md) lub [pakietu Enterprise Mobility + Security E5 w wersji próbnej](https://aka.ms/emse5trial).

## <a name="get-started-with-access-reviews"></a>Rozpoczynanie pracy za pomocą przeglądów dostępu

Aby dowiedzieć się więcej na temat tworzenia i wykonywanie przeglądów dostępu, obejrzyj ten krótki pokaz:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Jeśli wszystko jest gotowe do wdrożenia przeglądów dostępu w organizacji, wykonaj następujące kroki w wideo, aby dołączyć, szkolenie administratorów i Utwórz swój pierwszy przegląd dostępu!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="enable-access-reviews"></a>Włącz wyświetlanie przeglądów dostępu

Aby włączyć przeglądów dostępu, wykonaj następujące kroki.

1. Jako administrator globalny lub administrator użytkowników, zaloguj się do [witryny Azure portal](https://portal.azure.com) przeglądy dla której chcesz używać dostępu.

1. Kliknij przycisk **wszystkich usług** i znajdź usługę przeglądów dostępu.

1. Kliknij przycisk **przeglądów dostępu**.

    ![Wszystkie usługi — przeglądy dostępu](./media/access-reviews-overview/all-services-access-reviews.png)

1. Na liście w okienku nawigacji kliknij **dołączanie** otworzyć **Dołącz przeglądy dostępu** strony.

    ![Dołączanie przeglądy dostępu](./media/access-reviews-overview/onboard-button.png)

1. Kliknij przycisk **Utwórz** umożliwiając dostęp do przeglądów w bieżącym katalogu.

    ![Dołącz przeglądy dostępu](./media/access-reviews-overview/onboard-access-reviews.png)

    Przeglądy przy następnym uruchomieniu programu Microsoft access, opcje przeglądu dostępu zostaną włączone.

    ![Przeglądy dostępu włączone](./media/access-reviews-overview/access-reviews-enabled.png)

## <a name="next-steps"></a>Kolejne kroki

- [Tworzenie przeglądu dostępu grup lub aplikacji](create-access-review.md)
- [Tworzenie przeglądu dostępu użytkowników ról administracyjnych usługi Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Przegląd dostępu wszystkich użytkowników do grupy lub aplikacji](perform-access-review.md)
- [Kończenie przeglądu dostępu grup lub aplikacji](complete-access-review.md)
