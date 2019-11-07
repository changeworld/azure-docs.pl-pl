---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows — Azure
description: Przegląd rozwiązywania problemów podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: aa4254673d426579a5e5f0e90e389db7c6cadff0
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607347"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązania problemów.

## <a name="provide-feedback"></a>Przekazywanie opinii

Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="escalation-tracks"></a>Śledzenie eskalacji

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które mogą wystąpić podczas konfigurowania środowiska dzierżawy przy użyciu Pulpit zdalny klienta. Po skonfigurowaniu dzierżawy możesz użyć naszej nowej [usługi diagnostycznej](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) , aby zidentyfikować problemy dla typowych scenariuszy.

>[!NOTE]
> Mamy forum społeczności Tech., które można odwiedzić, aby omówić Twoje problemy z zespołem produktu i aktywnymi członkami społeczności. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby 

| **Wykonaj**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Tworzenie dzierżawy                                                    | Jeśli wystąpi awaria platformy Azure, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). w przeciwnym razie **Otwórz żądanie obsługi dla pulpitu wirtualnego systemu Windows (obliczenia)** .|
| Uzyskiwanie dostępu do szablonów witryny Marketplace w Azure Portal       | Jeśli wystąpi awaria platformy Azure, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). <br> <br> Szablony pulpitów wirtualnych systemu Windows w portalu Azure Marketplace są dostępne bezpłatnie.|
| Uzyskiwanie dostępu do szablonów Azure Resource Manager z usługi GitHub                                  | Zapoznaj się z sekcją "Tworzenie maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows" w temacie [Tworzenie dzierżaw i puli hostów](troubleshoot-set-up-issues.md). Jeśli problem nadal występuje, skontaktuj się z [zespołem pomocy technicznej usługi GitHub](https://github.com/contact). <br> <br> Jeśli błąd wystąpi po uzyskaniu dostępu do szablonu w usłudze GitHub, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).|
| Ustawienia puli hostów sesji Azure Virtual Network (VNET) i usługi Express Route               | Skontaktuj się z **pomocą techniczną platformy Azure (sieci)** . |
| Tworzenie maszyny wirtualnej puli hostów sesji, gdy Azure Resource Manager szablony udostępniane z pulpitem wirtualnym systemu Windows nie są używane | Skontaktuj się z **pomocą techniczną platformy Azure (obliczenia)** . <br> <br> Problemy z szablonami Azure Resource Manager dostarczanymi z pulpitem wirtualnym systemu Windows można znaleźć w sekcji Tworzenie dzierżawy pulpitu wirtualnego systemu Windows w ramach [tworzenia dzierżawy i puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji usług pulpitu wirtualnego systemu Windows z Azure Portal    | Skontaktuj się z **pomocą techniczną platformy Azure**. <br> <br> W przypadku problemów z zarządzaniem przy użyciu programu PowerShell Usługi pulpitu zdalnego/Windows Virtual Desktop, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub **Otwórz żądanie obsługi dla pulpitu wirtualnego systemu Windows (obliczenia)** . |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązane z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub **Otwórz żądanie obsługi dla pulpitu wirtualnego systemu Windows (obliczenia)** . <br> <br> Jeśli problemy są powiązane z przykładowym graficznym interfejsem użytkownika (GUI), skontaktuj się z społecznością usługi Yammer.|
| Klient pulpitu zdalnego działa nieprawidłowo przy uruchomieniu                                                 | Zobacz [pulpit zdalny połączenia klienckie](troubleshoot-client-connection.md) , a jeśli to nie rozwiąże problemu, **Otwórz żądanie obsługi dla pulpitu wirtualnego systemu Windows (obliczenia)** .  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Połączony, ale brak kanału informacyjnego                                                                 | Rozwiązywanie problemów przy użyciu opcji "użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)" w temacie [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji, **Otwórz żądanie obsługi dla pulpitu wirtualnego systemu Windows (obliczenia)** . |
| Problemy z odnajdywaniem źródła danych ze względu na sieć                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md) , a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi, zobacz [Środowisko pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek dotyczący rozwiązywania problemów, zobacz [Samouczek: Rozwiązywanie problemów z wdrożeniami szablonów Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
