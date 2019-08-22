---
title: Omówienie rozwiązywania problemów z pulpitem wirtualnym systemu Windows, opinie i pomoc techniczna — Azure
description: Przegląd rozwiązywania problemów podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: a31750d888410b9bdaea28bc5153152c426f1915
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877547"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

Ten artykuł zawiera omówienie problemów, które mogą wystąpić podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego systemu Windows i zapewnia sposoby rozwiązania problemów.

## <a name="provide-feedback"></a>Przekazywanie opinii

Obecnie nie zajmują się pomocą techniczną, gdy pulpit wirtualny systemu Windows jest w wersji zapoznawczej. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić usługę pulpitu wirtualnego systemu Windows z zespołem produktu i aktywnymi członkami społeczności.

## <a name="escalation-tracks"></a>Śledzenie eskalacji

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które mogą wystąpić podczas konfigurowania środowiska dzierżawy przy użyciu Pulpit zdalny klienta. Po skonfigurowaniu dzierżawy możesz użyć naszej nowej [usługi diagnostycznej](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) , aby zidentyfikować problemy dla typowych scenariuszy.

>[!NOTE]
>Obecnie nie zajmują się pomocą techniczną, gdy pulpit wirtualny systemu Windows jest w wersji zapoznawczej. Za każdym razem, gdy będziemy korzystać z pomocy technicznej pulpitu wirtualnego systemu Windows, odwiedź nasze forum społeczności Tech. Odwiedź [społeczność Tech. pulpitu wirtualnego systemu Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , aby omówić problemy z zespołem produktu i aktywnymi członkami społeczności. Jeśli konieczne jest rozwiązanie problemu z pomocą techniczną, należy uwzględnić identyfikator działania i przybliżony przedział czasu, w którym wystąpił problem.

| **Wykonaj**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Tworzenie dzierżawy                                                    | Jeśli wystąpi awaria platformy Azure, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). w przeciwnym razie skontaktuj się z **pomocą techniczną usługi pulpitu zdalnego/pulpitu wirtualnego systemu Windows**.|
| Uzyskiwanie dostępu do szablonów witryny Marketplace w Azure Portal       | Jeśli wystąpi awaria platformy Azure, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/). <br> <br> Szablony pulpitów wirtualnych systemu Windows w portalu Azure Marketplace są dostępne bezpłatnie.|
| Uzyskiwanie dostępu do szablonów Azure Resource Manager z usługi GitHub                                  | Zapoznaj się z sekcją "Tworzenie maszyn wirtualnych hosta sesji usług pulpitu wirtualnego systemu Windows" w temacie [Tworzenie dzierżaw i puli hostów](troubleshoot-set-up-issues.md). Jeśli problem nadal występuje, skontaktuj się z [zespołem pomocy technicznej usługi GitHub](https://github.com/contact). <br> <br> Jeśli błąd wystąpi po uzyskaniu dostępu do szablonu w usłudze GitHub, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).|
| Ustawienia puli hostów sesji Azure Virtual Network (VNET) i usługi Express Route               | Skontaktuj się z **pomocą techniczną platformy Azure (sieci)** . |
| Tworzenie maszyny wirtualnej puli hostów sesji, gdy Azure Resource Manager szablony udostępniane z pulpitem wirtualnym systemu Windows nie są używane | Skontaktuj się z **pomocą techniczną platformy Azure (obliczenia)** . <br> <br> Problemy z szablonami Azure Resource Manager dostarczanymi z pulpitem wirtualnym systemu Windows można znaleźć w sekcji Tworzenie dzierżawy pulpitu wirtualnego systemu Windows w ramach [tworzenia dzierżawy i puli hostów](troubleshoot-set-up-issues.md). |
| Zarządzanie środowiskiem hosta sesji usług pulpitu wirtualnego systemu Windows z Azure Portal    | Skontaktuj się z **pomocą techniczną platformy Azure**. <br> <br> W przypadku problemów z zarządzaniem przy użyciu programu PowerShell Usługi pulpitu zdalnego/Windows Virtual Desktop można znaleźć w temacie [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md) lub skontaktować się z **zespołem pomocy technicznej pulpitu usługi pulpitu zdalnego/systemu Windows**. |
| Zarządzanie konfiguracją pulpitu wirtualnego systemu Windows powiązane z pulami hostów i grupami aplikacji (grupami aplikacji)      | Zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md)lub skontaktuj się z **zespołem pomocy technicznej usług pulpitu wirtualnego (usługi pulpitu zdalnego/Windows**). <br> <br> Jeśli problemy są powiązane z przykładowym graficznym interfejsem użytkownika (GUI), skontaktuj się z społecznością usługi Yammer.|
| Klient pulpitu zdalnego działa nieprawidłowo przy uruchomieniu                                                 | Zobacz [pulpit zdalny połączenia klienckie](troubleshoot-client-connection.md) , a jeśli to nie rozwiąże problemu, skontaktuj się z **pomocą techniczną usługi pulpitu zdalnego/Team Desktop dla systemu Windows**.  <br> <br> Jeśli jest to problem z siecią, użytkownicy muszą skontaktować się z administratorem sieci. |
| Połączony, ale brak kanału informacyjnego                                                                 | Rozwiązywanie problemów przy użyciu opcji "użytkownik nawiązuje połączenie, ale nic nie jest wyświetlane (brak źródła danych)" w temacie [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md). <br> <br> Jeśli użytkownicy zostali przypisani do grupy aplikacji, należy eskalować do **zespołu pomocy technicznej dla usługi pulpitu zdalnego/pulpitu wirtualnego systemu Windows**. |
| Problemy z odnajdywaniem źródła danych ze względu na sieć                                            | Użytkownicy muszą skontaktować się z administratorem sieci. |
| Łączenie klientów                                                                    | Zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md) , a jeśli to nie rozwiąże problemu, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnych lub pulpitu                                      | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |
| Komunikaty lub błędy licencjonowania                                                          | Jeśli problemy są powiązane z konkretną aplikacją lub produktem, skontaktuj się z zespołem odpowiedzialnym za ten produkt. |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązać problemy podczas tworzenia dzierżawy i puli hostów w środowisku pulpitu wirtualnego systemu Windows, zobacz [Tworzenie dzierżawy i puli hostów](troubleshoot-set-up-issues.md).
- Aby rozwiązać problemy podczas konfigurowania maszyny wirtualnej w programie Virtual Desktop systemu Windows, zobacz [Konfiguracja maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązać problemy z połączeniami klienta pulpitu wirtualnego systemu Windows, zobacz [pulpit zdalny połączenia klientów](troubleshoot-client-connection.md).
- Aby rozwiązać problemy występujące podczas korzystania z programu PowerShell z pulpitem wirtualnym systemu Windows, zobacz [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [środowisko Windows Virtual Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek dotyczący rozwiązywania [problemów, zobacz Samouczek: Rozwiązywanie problemów z](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)wdrożeniami szablonów Menedżer zasobów.
- Aby dowiedzieć się więcej o akcjach inspekcji, zobacz [Inspekcja operacji przy użyciu Menedżer zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach dotyczących określania błędów podczas wdrażania, zobacz [Wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).