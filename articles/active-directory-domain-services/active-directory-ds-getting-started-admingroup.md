---
title: 'Usługi Azure Active Directory Domain Services: Wprowadzenie | Dokumentacja firmy Microsoft'
description: Włączanie usługi Azure Active Directory Domain Services w witrynie Azure portal
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: maheshu
ms.openlocfilehash: 2290273c1b998a2d75046fcbcf613762ddd588ee
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503210"
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

    ![Usługi domenowe — karta Przegląd podczas stan aprowizacji ](./media/getting-started/domain-services-provisioning-state-details.png)

4. Po pełnym zaaprowizowaniu domeny zarządzanej, **Przegląd** karta przedstawia stan domeny jako **systemem**.

    ![Usługi Domain Services — karta Przegląd po pełnej aprowizacji](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Podczas procesu inicjowania obsługi administracyjnej Azure AD Domain Services tworzy aplikacje dla przedsiębiorstw o nazwie "Usługi kontrolera domeny" i "AzureActiveDirectoryDomainControllerServices" w katalogu. Te aplikacje dla przedsiębiorstw są wymagane do obsługi Twojej domeny zarządzanej. Należy bezwzględnie te nie są usuwane w dowolnym momencie.
    >

5. Na **właściwości** kartę, zobaczysz dwa adresy IP, w której domeny kontrolerów są dostępne dla sieci wirtualnej.

    ![Usługi domenowe — karta właściwości po pełnej aprowizacji](./media/getting-started/domain-services-provisioned-properties.png)


## <a name="need-help"></a>Potrzebujesz pomocy?
Może potrwać godzinę lub dwie dla obu kontrolerów domeny dla domeny zarządzanej do zainicjowania obsługi administracyjnej. Jeśli wdrożenie nie powiodło się lub została zablokowana w stanie "Oczekiwanie" dla więcej niż kilka godzin, swobodnie [skontaktuj się z zespołem produktu, aby uzyskać Pomoc](active-directory-ds-contact-us.md).


## <a name="next-step"></a>Następny krok
[Zadanie 4. Aktualizowanie ustawień usługi DNS dla sieci wirtualnej platformy Azure](active-directory-ds-getting-started-dns.md)
