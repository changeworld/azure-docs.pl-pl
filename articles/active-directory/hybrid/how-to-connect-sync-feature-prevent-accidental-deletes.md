---
title: 'Synchronizacja usługi Azure AD Connect: zapobieganie przypadkowym usuwaniom | Dokumenty firmy Microsoft'
description: W tym temacie opisano funkcję zapobiegania przypadkowym usuwaniom (zapobiegając przypadkowym usunięciu) w usłudze Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 6b852cb4-2850-40a1-8280-8724081601f7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f3109b4c87e25444629ca25411894eab8a9d56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71827139"
---
# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Synchronizacja programu Azure AD Connect: zapobieganie przypadkowemu usuwaniu
W tym temacie opisano funkcję zapobiegania przypadkowym usuwaniom (zapobiegając przypadkowym usunięciu) w usłudze Azure AD Connect.

Podczas instalowania usługi Azure AD Connect zapobieganie przypadkowym usuwaniom jest domyślnie włączone i skonfigurowane tak, aby nie zezwalać na eksport z więcej niż 500 usuwaniami. Ta funkcja została zaprojektowana po to, aby zapewnić ochronę przed przypadkowymi zmianami konfiguracji, a także zmianami w katalogu lokalnym, które miałyby wpływ na wielu użytkowników i inne obiekty.

## <a name="what-is-prevent-accidental-deletes"></a>Co zapobiega przypadkowym usuwaniu
Typowe scenariusze, gdy widzisz wiele usuwa obejmują:

* Zmiany [w filtrowaniu,](how-to-connect-sync-configure-filtering.md) w którym cała [jednostka organizacyjna](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) lub [domena](how-to-connect-sync-configure-filtering.md#domain-based-filtering) nie jest zaznaczona.
* Wszystkie obiekty w jednostce organizacyjnej zostały usunięte.
* Nazwa jednostki organizacyjnej została zmieniona, więc wszystkie obiekty w tej jednostce są uznawane za znajdujące się poza zakresem synchronizacji.

Domyślną wartość 500 obiektów można zmienić `Enable-ADSyncExportDeletionThreshold`za pomocą programu PowerShell przy użyciu programu , który jest częścią modułu ad sync zainstalowanego za pomocą usługi Azure Active Directory Connect. Tę wartość należy skonfigurować tak, aby odpowiadała rozmiarowi organizacji. Ponieważ harmonogram synchronizacji jest uruchamiany co 30 minut, wartość jest liczbą usuwanych widocznych w ciągu 30 minut.

Jeśli istnieje zbyt wiele usuwania przemieszczane do wyeksportowania do usługi Azure AD, a następnie eksport zatrzymuje się i otrzymasz wiadomość e-mail w ten sposób:

![Zapobiegaj przypadkowemu usunięciu wiadomości e-mail](./media/how-to-connect-sync-feature-prevent-accidental-deletes/email.png)

> *Witam (kontakt techniczny). W (czasie) usługa synchronizacji tożsamości wykryła, że liczba usunięć przekroczyła skonfigurowany próg usunięcia dla (nazwa organizacji). W tym przebiegu synchronizacji tożsamości wysłano łącznie (liczba) obiektów do usunięcia. To spełnione lub przekroczone skonfigurowaną wartość progową usuwania (liczba) obiektów. Musimy dostarczyć potwierdzenie, że te usunięcia powinny być przetwarzane, zanim będziemy kontynuować. Aby uzyskać więcej informacji na temat błędu wymienionego w tej wiadomości e-mail, zobacz zapobieganie przypadkowym usunięciu.*
>
> 

Stan można również `stopped-deletion-threshold-exceeded` zobaczyć podczas wyszukiwania w interfejsie użytkownika **programu Synchronizacja menedżera usług** dla profilu eksportu.
![Zapobiegaj przypadkowym usunięciu interfejsu użytkownika menedżera usługi synchronizacji](./media/how-to-connect-sync-feature-prevent-accidental-deletes/syncservicemanager.png)

Jeśli było to nieoczekiwane, zbadaj i podejmij działania naprawcze. Aby zobaczyć, które obiekty mają zostać usunięte, wykonaj następujące czynności:

1. Uruchom **usługę synchronizacji** z menu Start.
2. Przejdź do **łączników**.
3. Wybierz łącznik z **typem usługi Azure Active Directory**.
4. W obszarze **Akcje** po prawej stronie wybierz pozycję **Obszar łącznika wyszukiwania**.
5. W wyskakującym okienku w obszarze **Zakres**wybierz pozycję **Odłączone od** i wybierz czas w przeszłości. Kliknij przycisk **Wyszukaj**. Ta strona zawiera widok wszystkich obiektów, które mają zostać usunięte. Klikając każdy element, można uzyskać dodatkowe informacje o obiekcie. Można również kliknąć pozycję **Ustawienia kolumn,** aby dodać dodatkowe atrybuty, które mają być widoczne w siatce.

![Miejsce łącznika wyszukiwania](./media/how-to-connect-sync-feature-prevent-accidental-deletes/searchcs.png)

[!NOTE] Jeśli nie masz pewności, że wszystkie usunięcia są pożądane i chcesz zejść bezpieczniejszą trasą. Można użyć polecenia cmdlet programu `Enable-ADSyncExportDeletionThreshold` PowerShell : aby ustawić nowy próg, zamiast wyłączać próg, który może umożliwić niepożądane usunięcia. 

## <a name="if-all-deletes-are-desired"></a>Jeśli wszystkie usunięcia są pożądane
Jeśli wszystkie usunięcia są pożądane, wykonaj następujące czynności:

1. Aby pobrać bieżący próg usunięcia, uruchom polecenie cmdlet programu PowerShell `Get-ADSyncExportDeletionThreshold`. Podaj konto i hasło administratora globalnego usługi Azure AD. Wartość domyślna to 500.
2. Aby tymczasowo wyłączyć tę ochronę i umożliwić te usunięcia przejść, uruchom `Disable-ADSyncExportDeletionThreshold`polecenie cmdlet programu PowerShell: . Podaj konto i hasło administratora globalnego usługi Azure AD.
   ![Poświadczenia](./media/how-to-connect-sync-feature-prevent-accidental-deletes/credentials.png)
3. Po wybraniu łącznika usługi Azure Active Directory wybierz akcję **Uruchom** i wybierz pozycję **Eksportuj**.
4. Aby ponownie włączyć ochronę, uruchom polecenie cmdlet `Enable-ADSyncExportDeletionThreshold -DeletionThreshold 500`programu PowerShell: . Zastąp 500 wartością, którą zauważyłeś podczas pobierania bieżącego progu usunięcia. Podaj konto i hasło administratora globalnego usługi Azure AD.

## <a name="next-steps"></a>Następne kroki
**Tematy omówienie**

* [Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
