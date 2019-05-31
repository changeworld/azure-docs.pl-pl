---
title: 'Azure Active Directory Domain Services: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4a515f3d669794d5bcdd8fa506a8d3b846db987
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234936"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal

## <a name="task-3-configure-administrative-group"></a>Zadanie 3: Konfigurowanie grupy administracyjnej

W tym zadaniu konfiguracji utworzysz grupy administracyjnej w katalogu usługi Azure AD. Nosi nazwę tej grupy administracyjnej specjalne *Administratorzy usługi AAD DC*. Członkowie tej grupy są przyznawane uprawnienia administracyjne na komputerach, które są przyłączone do domeny zarządzanej. Na komputerach przyłączonych do domeny ta grupa jest dodawana do grupy Administratorzy. Ponadto Członkowie tej grupy można użyć usług pulpitu zdalnego można łączyć się zdalnie z komputerów przyłączonych do domeny.

> [!NOTE]
> Nie masz uprawnienia administratora domeny lub administratora przedsiębiorstwa w domenie zarządzanej, który został utworzony przy użyciu usługi Azure Active Directory Domain Services. W domenach zarządzanych te uprawnienia są zarezerwowane przez usługę i nie są udostępniane użytkownikom w ramach dzierżawy. Jednak można użyć specjalnej grupy administracyjne utworzone w tym zadaniu konfiguracji można wykonywać niektóre operacje uprzywilejowane. Operacje te obejmują przyłączanie komputerów do domeny, należącego do grupy administracyjnej na komputerach przyłączonych do domeny i konfigurowanie zasad grupy.
>

Kreator automatycznie tworzy grupy administracyjnej w katalogu usługi Azure AD. Ta grupa jest nazywany "Administratorzy usługi AAD DC". Jeśli masz istniejącą grupę o tej nazwie w katalogu usługi Azure AD, Kreator wybierze tej grupy. Można skonfigurować za pomocą członkostwa w grupie **grupy Administrator** strony kreatora.

1. Aby skonfigurować członkostwo w grupie, kliknij **Administratorzy usługi AAD DC**.

    ![Konfiguruj członkostwo w grupach](./media/getting-started/domain-services-blade-admingroup.png)

2. Kliknij przycisk **dodawać członków** przycisk, aby dodać użytkowników z katalogu usługi Azure AD do grupy administratorów.

3. Gdy wszystko będzie gotowe, kliknij przycisk **OK** można przenieść do **Podsumowanie** strony kreatora.

## <a name="configure-synchronization"></a>Konfigurowanie synchronizacji

Azure AD Domain Services umożliwia albo pełną synchronizację wszystkich użytkowników i grup w usłudze Azure AD, lub wybrać synchronizacji o określonym zakresie, aby zsynchronizować tylko określone grupy. Jeśli wybierzesz opcję pełnej synchronizacji, zostanie **nie** można wybrać synchronizacji o określonym zakresie w późniejszym czasie. Aby dowiedzieć się więcej o zakresie synchronizacji, odwiedź stronę [usługi Azure AD Domain Services o określonym zakresie synchronizacji artykułu](scoped-synchronization.md).

### <a name="full-synchronization"></a>Pełna synchronizacja

1. Pełnej synchronizacji, po prostu kliknij przycisk "OK" w dolnej części ekranu, jako pełne nie została jeszcze wybrana.
    ![Pełna synchronizacja](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>O określonym zakresie synchronizacji

1. Przełącz przycisk synchronizacji "W polu" i strona grupy będą wyświetlane. Z tego zobaczysz, jakie grupy zostały już wybrane do synchronizacji z domeną zarządzaną.
    ![O określonym zakresie synchronizacji](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Kliknij przycisk **wybierz grupy** w górnym pasku nawigacyjnym. W tym miejscu selektor grupy wyskakującym zostanie wyświetlona na tej stronie. Użyj tego, aby zaznaczyć dodatkowe grupy, aby zsynchronizować z usługą Azure AD Domain Services. Po zakończeniu kliknij przycisk **wybierz** zamknąć Selektor grupy, a następnie dodaj te grupy do wybranej listy.
    ![Wybierz grupę, w zakresie synchronizacji](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Kliknij przycisk **OK** aby przejść do strony podsumowania.

## <a name="deploy-your-managed-domain"></a>Wdrażanie Twojej domeny zarządzanej

1. Na **Podsumowanie** strony kreatora przejrzyj ustawienia konfiguracji dla domeny zarządzanej. Możesz przejść wstecz do którykolwiek z kroków kreatora, aby wprowadzić zmiany, jeśli to konieczne. Gdy wszystko będzie gotowe, kliknij przycisk **OK** do utworzenia nowej domeny zarządzanej.

    ![Podsumowanie](./media/getting-started/domain-services-blade-summary.png)

2. Zostanie wyświetlone powiadomienie, która przedstawia postęp wdrożenia usług domenowych Azure AD. Kliknij powiadomienie, aby wyświetlić szczegółowe informacje o postępie wdrożenia.

    ![Powiadomienie — wdrażanie jest w toku](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Sprawdź stan wdrożenia Twojej domeny zarządzanej

Proces udostępniania Twoja domena zarządzana może potrwać do godziny.

1. Gdy wdrożenie jest w toku, możesz wyszukać "domain services", w **Wyszukaj zasoby** pola wyszukiwania. Wybierz **usług domenowych Azure AD** z wyników wyszukiwania. **Usług domenowych Azure AD** bloku Wyświetla domeny zarządzanej, która jest aprowizowana.

    ![Znajdź trwa aprowizowanie domeny zarządzanej](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Kliknij nazwę domeny zarządzanej (na przykład "contoso100.com"), aby zobaczyć więcej szczegółów na temat domeny zarządzanej.

    ![Usługi domenowe — stan aprowizacji](./media/getting-started/domain-services-provisioning-state.png)

3. **Przegląd** karta pokazuje, że domena zarządzana jest aktualnie aprowizowany. Nie można skonfigurować domeny zarządzanej, dopóki nie jest w pełni zaaprowizowanym. Może potrwać do godziny dla Twojej domeny zarządzanej być w pełni zaaprowizowanym.

    ![Usługi domenowe — karta Przegląd podczas stan aprowizacji](./media/getting-started/domain-services-provisioning-state-details.png)

4. Po pełnym zaaprowizowaniu domeny zarządzanej, **Przegląd** karta przedstawia stan domeny jako **systemem**.

    ![Usługi Domain Services — karta Przegląd po pełnej aprowizacji](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Podczas procesu inicjowania obsługi administracyjnej Azure AD Domain Services tworzy aplikacje dla przedsiębiorstw o nazwie "Usługi kontrolera domeny" i "AzureActiveDirectoryDomainControllerServices" w katalogu. Te aplikacje dla przedsiębiorstw są wymagane do obsługi Twojej domeny zarządzanej. Należy bezwzględnie te nie są usuwane w dowolnym momencie.
    >

5. Na **właściwości** kartę, zobaczysz dwa adresy IP, w której domeny kontrolerów są dostępne dla sieci wirtualnej.

    ![Usługi domenowe — karta właściwości po pełnej aprowizacji](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Potrzebujesz pomocy?

Może potrwać godzinę lub dwie dla obu kontrolerów domeny dla domeny zarządzanej do zainicjowania obsługi administracyjnej. Jeśli wdrożenie nie powiodło się lub została zablokowana w stanie "Oczekiwanie" dla więcej niż kilka godzin, swobodnie [skontaktuj się z zespołem produktu, aby uzyskać Pomoc](contact-us.md).

## <a name="next-step"></a>Następny krok

[Zadanie 4. Aktualizowanie ustawień usługi DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
