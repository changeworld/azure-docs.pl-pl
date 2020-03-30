---
title: Narzędzie do zarządzania pulpitem wirtualnym systemu Windows — platforma Azure
description: Jak rozwiązywać problemy z narzędziem do zarządzania pulpitem wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127483"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Rozwiązywanie problemów z narzędziem zarządzania Windows Virtual Desktop

W tym artykule opisano problemy, które mogą wystąpić podczas wdrażania narzędzia do zarządzania pulpitem wirtualnym systemu Windows i jak je rozwiązać.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Błąd: usługi narzędzi do zarządzania skonfigurowane, ale automatyczna instalacja kończy się niepowodzeniem

Po pomyślnym skonfigurowaniu usług dla narzędzia do zarządzania, ale instalacja automatyczna nie powiedzie się, zostanie wyświetlony ten komunikat o błędzie:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Zwykle oznacza to jedną z następujących dwóch rzeczy:

- Użytkownik ma uprawnienia właściciela do swojej subskrypcji i administratora globalnego na poziomie dzierżawy, ale nie może zalogować się na platformie Azure.
- Ustawienia konta użytkownika mają włączone uwierzytelnianie wieloskładnikowe.

Aby rozwiązać ten problem:

1. Upewnij się, że użytkownik utworzony dla głównej nazwy użytkownika usługi Azure Active Directory ma poziom subskrypcji "Współautor".
2. Zaloguj się, aby <portal.azure.com> za pomocą konta UPN, aby sprawdzić ustawienia konta i upewnić się, że uwierzytelnianie wieloskładnikowe nie jest włączone. Jeśli jest włączona, wyłącz ją.
3. Odwiedź stronę Zgoda pulpitu wirtualnego systemu Windows i upewnij się, że serwer i aplikacje klienckie mają zgodę.
4. Przejrzyj samouczek [Wdrażanie narzędzia do zarządzania,](manage-resources-using-ui.md) jeśli problem będzie kontynuowany i ponownie wdrożyć narzędzie.

## <a name="error-job-with-specified-id-already-exists"></a>Błąd: Zadanie o określonym identyfikatorze już istnieje

Jeśli użytkownik zobaczy komunikat o błędzie "Zadanie o określonym identyfikatorze już istnieje", to dlatego, że nie podał unikatowej nazwy w parametrze "Nazwa aplikacji" podczas wdrażania szablonu.

Aby rozwiązać ten problem, ponownie wdrożyć narzędzie do zarządzania z parametrem "Nazwa aplikacji" wypełnione.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Monit o opóźnioną zgodę podczas otwierania narzędzia do zarządzania

Po wdrożeniu narzędzia do zarządzania monit o zgodę może nie zostać otwarty od razu. Oznacza to, że usługa aplikacji sieci Web platformy Azure trwa dłużej niż zwykle, aby załadować. Monit powinien pojawić się po zakończeniu ładowania usługi Azure Web.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>Użytkownik nie może wdrożyć narzędzia do zarządzania w regionie Wschodnie stany USA

Jeśli klient ustawia region na wschodnie stany USA, nie może wdrożyć narzędzia do zarządzania.

Aby rozwiązać ten problem, należy wdrożyć narzędzie do zarządzania w innym regionie. Ponowne wdrożenie narzędzia w innym regionie nie powinno mieć wpływu na środowisko użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o ścieżkach eskalacji, [aby zapoznać się z omówieniem rozwiązywania problemów, opiniami i pomocą techniczną.](troubleshoot-set-up-overview.md)
- Dowiedz się, jak zgłaszać problemy z narzędziami pulpitu wirtualnego systemu Windows w [aplikacji Szablony usług pulpitu zdalnego w systemie ARM](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- Aby zapoznać się z omówieniem rozwiązywania problemów z pulpitem wirtualnym systemu Windows i ścieżkami eskalacji, zobacz [Omówienie rozwiązywania problemów, opinie i pomoc techniczna](troubleshoot-set-up-overview.md).
- Aby dowiedzieć się, jak wdrożyć narzędzie do zarządzania, zobacz [Wdrażanie narzędzia do zarządzania](manage-resources-using-ui.md).