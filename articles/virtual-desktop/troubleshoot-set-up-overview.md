---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows — platforma Azure
description: Omówienie rozwiązywania problemów podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127402"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows i zawiera sposoby rozwiązania problemów.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność techniczną pulpitu wirtualnego systemu Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby omówić usługę Pulpit wirtualny systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="escalation-tracks"></a>Ścieżki eskalacji

Poniższa tabela służy do identyfikowania i rozwiązywania problemów, które mogą wystąpić podczas konfigurowania środowiska dzierżawy przy użyciu klienta pulpitu zdalnego. Po skonfigurowaniu dzierżawy można użyć naszej nowej [usługi diagnostyki](diagnostics-role-service.md) do identyfikowania problemów dla typowych scenariuszy.

>[!NOTE]
> Mamy forum społeczności technologicznej, które możesz odwiedzić, aby omówić swoje problemy z zespołem produktu i aktywnymi członkami społeczności. Odwiedź [społeczność techniczną pulpitu wirtualnego systemu Windows,](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby rozpocząć dyskusję.

| **Problem**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Tworzenie dzierżawy pulpitu wirtualnego systemu Windows                                                    | Jeśli występuje awaria platformy Azure, [otwórz żądanie pomocy technicznej platformy Azure;](https://azure.microsoft.com/support/create-ticket/) w przeciwnym razie [otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pulpit **wirtualny systemu Windows** dla usługi, wybierz pozycję **Wdrożenie** dla typu problemu, a następnie wybierz pozycję Problemy **z utworzeniem dzierżawy pulpitu wirtualnego systemu Windows** dla podtypu problemu.|
| Uzyskiwanie dostępu do szablonów portalu portalu Azure       | Jeśli występuje awaria platformy Azure, [otwórz żądanie pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/create-ticket/) <br> <br> Szablony pulpitu wirtualnego systemu Azure Marketplace Windows są swobodnie dostępne.|
| Uzyskiwanie dostępu do szablonów usługi Azure Resource Manager z usługi GitHub                                  | Zobacz sekcję [Tworzenie maszyn wirtualnych hosta sesji pulpitu wirtualnego systemu Windows](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms) w programie Tenant i tworzenie puli [hostów](troubleshoot-set-up-issues.md). Jeśli problem nadal nie został rozwiązany, skontaktuj się z [zespołem pomocy technicznej usługi GitHub](https://github.com/contact). <br> <br> Jeśli błąd wystąpi po przejściu do szablonu w usłudze GitHub, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/create-ticket/).|
| Ustawienia hosta sesji platformy Azure Virtual Network (VNET) i express route               | [Otwórz żądanie pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/create-ticket/)a następnie wybierz odpowiednią usługę (w kategorii Sieć). |
| Tworzenie maszyny wirtualnej (VM) puli hostów sesji, gdy szablony usługi Azure Resource Manager dostarczane z pulpitem wirtualnym systemu Windows nie są używane | [Otwórz żądanie pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/create-ticket/)a następnie wybierz **maszynę wirtualną z systemem Windows** dla usługi. <br> <br> Aby uzyskać problemy z szablonami Usługi Azure Resource Manager, które są dostarczane z pulpitem wirtualnym systemu Windows, zobacz Tworzenie sekcji dzierżawy pulpitu wirtualnego systemu Windows [w obszarze Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji pulpitu wirtualnego systemu Windows z witryny Azure portal    | [Otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Aby uzyskać problemy z zarządzaniem podczas korzystania z programu PowerShell usług pulpitu zdalnego/pulpitu wirtualnego systemu Windows, zobacz [Program Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub otwórz żądanie pomocy technicznej platformy [Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **Pulpit wirtualny systemu Windows** dla usługi, wybierz pozycję **Konfiguracja i zarządzanie** dla typu problemu, a następnie wybierz pozycję Problemy **konfigurujące dzierżawę przy użyciu programu PowerShell** dla podtypu problemu. |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązaną z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Program Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub otwórz żądanie pomocy technicznej platformy [Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję **Pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz odpowiedni typ problemu.|
| Wdrażanie kontenerów profili FSLogix i zarządzanie nimi | Zobacz [Przewodnik rozwiązywania problemów z produktami FSLogix,](/fslogix/fslogix-trouble-shooting-ht/) a jeśli to nie [rozwiąże](https://azure.microsoft.com/support/create-ticket/)problemu, Otwórz żądanie pomocy technicznej platformy Azure , wybierz pulpit **wirtualny systemu Windows** dla usługi, wybierz **FSLogix** dla typu problemu, a następnie wybierz odpowiedni podtyp problemu. |
| Awaria klientów pulpitu zdalnego podczas uruchamiania                                                 | Zobacz [Rozwiązywanie problemów z klientem pulpitu zdalnego,](troubleshoot-client.md) a jeśli to nie [rozwiąże](https://azure.microsoft.com/support/create-ticket/)problemu, Otwórz żądanie pomocy technicznej platformy Azure , wybierz pozycję **Pulpit wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję Klienci **pulpitu zdalnego** dla typu problemu.  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Podłączony, ale bez podawania                                                                 | Rozwiązywanie problemów z używaniem [połączenia użytkownika, ale nic nie jest wyświetlane (bez źródła)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) w [połączeniach usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji, [otwórz żądanie pomocy technicznej platformy Azure](https://azure.microsoft.com/support/create-ticket/), wybierz pozycję Pulpit **wirtualny systemu Windows** dla usługi, a następnie wybierz pozycję Klienci **pulpitu zdalnego** dla typu problemu. |
| Problemy z odnajdywania kanałów z powodu sieci                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [Połączenia usługi pulpitu wirtualnego systemu Windows,](troubleshoot-service-connection.md) a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Responsywność aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z określoną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z określoną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Problemy podczas korzystania z narzędzi pulpitu wirtualnego systemu Windows w usłudze GitHub (szablony usługi Azure Resource Manager, narzędzie diagnostyczne, narzędzie do zarządzania) | Aby zgłosić problemy, zobacz [Szablony usługi Azure Resource Manager dla usług pulpitu zdalnego.](https://github.com/Azure/RDS-Templates/blob/master/README.md) |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia puli dzierżawy i hosta w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie puli dzierżawy i hosta](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej (VM) na pulpicie wirtualnym systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klientów pulpitu wirtualnego systemu Windows, zobacz [Połączenia usługi pulpitu wirtualnego systemu Windows](troubleshoot-service-connection.md).
- Aby rozwiązać problemy z klientami usług pulpitu zdalnego, zobacz [Rozwiązywanie problemów z klientem pulpitu zdalnego](troubleshoot-client.md)
- Aby rozwiązać problemy podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Pulpit wirtualny systemu Windows PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej o usłudze, zobacz [Środowisko pulpitu wirtualnego systemu Windows](environment-setup.md).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrażaniem szablonów Menedżera zasobów](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji za pomocą Menedżera zasobów](../azure-resource-manager/management/view-activity-logs.md).
- Aby dowiedzieć się więcej o akcjach określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](../azure-resource-manager/templates/deployment-history.md).
