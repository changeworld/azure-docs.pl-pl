---
title: Windows pulpitu wirtualnego, rozwiązywanie problemów — omówienie, opinie i pomoc techniczna — platformy Azure
description: Przegląd problemów podczas konfigurowania środowiska dzierżawy Windows pulpitu wirtualnego.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b349d7f384c801e95d745f7a18535f9f5d13cf86
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605208"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>Rozwiązywania problemów — omówienie, opinie i obsługa techniczna

Ten artykuł zawiera omówienie problemy mogą wystąpić podczas konfigurowania środowiska dzierżawy pulpitu wirtualnego Windows i zawiera sposoby rozwiązania problemów.

## <a name="provide-feedback"></a>Przekazywanie opinii

Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) w celu omówienia usługi Windows pulpitu wirtualnego przy użyciu zespół pracujący nad produktem i elementów członkowskich aktywnej społeczności użytkowników.

## <a name="escalation-tracks"></a>Śledzi eskalacji

Skorzystaj z poniższej tabeli, aby zidentyfikować i rozwiązać problemy, które można napotkać podczas konfigurowania środowiska dzierżawy, przy użyciu klienta pulpitu zdalnego. Po skonfigurowaniu przez dzierżawców można użyć naszej nowej [Usługa diagnostyczna](https://docs.microsoft.com/azure/virtual-desktop/diagnostics-role-service) umożliwiają zidentyfikowanie problemów dla typowych scenariuszy.

>[!NOTE]
>Firma Microsoft obecnie nie są zbyt przypadki pomocy technicznej Windows pulpitu wirtualnego jest dostępna w wersji zapoznawczej. Zawsze, gdy będziemy odwoływać się do pomocy technicznej Windows pulpitu wirtualnego, przejdź na naszym forum społeczności technicznej teraz. Odwiedź stronę [społeczności technicznej pulpitu wirtualnego Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) aby omówić problemy związane z zespołu pracującego nad produktem i elementów członkowskich aktywnej społeczności użytkowników. Jeśli trzeba rozwiązać problemu wymagającego pomocy technicznej, należy uwzględnić identyfikator działania oraz przybliżony przedział czasu, w przypadku wystąpienia problemu.

| **Problem**                                                            | **Sugerowane rozwiązanie**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Tworzenie dzierżawy                                                    | W przypadku awarii platformy Azure, skontaktuj się z pomocą [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/); w przeciwnym razie skontaktuj się z pomocą **obsługę zdalnego pulpitu wirtualnego usług/Windows Desktop**.|
| Uzyskiwanie dostępu do portalu Marketplace szablony w witrynie Azure portal       | W przypadku awarii platformy Azure, skontaktuj się z pomocą [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/). <br> <br> Szablony platformy Azure pulpit wirtualny w Windows Marketplace są dostępne bezpłatnie.|
| Uzyskiwanie dostępu do usługi Azure Resource Manager z usługi GitHub                                  | W sekcji "Tworzenie Windows Virtual host sesji usług pulpitu maszyn wirtualnych" [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md). Jeśli nadal nierozwiązany problem, skontaktuj się z [zespołem pomocy technicznej w witrynie GitHub](https://github.com/contact). <br> <br> Jeśli błąd wystąpi po uzyskaniu dostępu do szablonu w usłudze GitHub, skontaktuj się z [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/).|
| Ustawienia hosta sesji puli usługi Azure Virtual Network (VNET) i Express Route               | Skontaktuj się z pomocą **pomocy technicznej platformy Azure (sieć)** . |
| Pula hosta sesji tworzenia maszyn wirtualnych (VM), gdy nie są używane szablony usługi Azure Resource Manager dostarczane z pulpitem wirtualnym Windows | Skontaktuj się z pomocą **pomocy technicznej platformy Azure (składnik obliczeniowy)** . <br> <br> W przypadku problemów z szablonami usługi Azure Resource Manager, które są dostarczane z Windows pulpitu wirtualnego, w sekcji Tworzenie wirtualnego pulpitu Windows dzierżawy [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md). |
| Zarządzanie środowisku hosta sesji pulpitu wirtualnego Windows w witrynie Azure portal    | Skontaktuj się z pomocą **pomocy technicznej platformy Azure**. <br> <br> Dotycząca problemów z zarządzaniem przy użyciu zdalnego pulpitu usług/Windows wirtualnego pulpitu programu PowerShell, zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md) lub skontaktuj się z **zespołem pomocy technicznej zdalnego pulpitu wirtualnego usług/Windows Desktop** . |
| Zarządzanie konfiguracją pulpitu wirtualnego Windows powiązane z hostów, pul i aplikacji (grup aplikacji)      | Zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md), lub skontaktuj się z **zespołem pomocy technicznej zdalnego pulpitu wirtualnego usług/Windows Desktop**. <br> <br> Jeśli problemy, które są powiązane próbki graficznego interfejsu użytkownika (GUI), korzystał z usługi Yammer dla społeczności.|
| Klienci usług pulpitu zdalnego usterek w menu start                                                 | Zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md) i jeśli to nie rozwiąże problemu, skontaktuj się z **zespołem pomocy technicznej zdalnego pulpitu wirtualnego usług/Windows Desktop**.  <br> <br> Jeśli jest problem z siecią, użytkownicy musieli skontaktować się z administratorem sieci, ich. |
| Połączona, ale nie źródło danych                                                                 | Rozwiązywanie problemów przy użyciu "łączy się użytkownik, ale będą wyświetlane żadne informacje (bez podawania)" sekcji [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md). <br> <br> Jeśli użytkownicy zostały przypisane do grupy aplikacji, przekazać do **zespołem pomocy technicznej zdalnego pulpitu wirtualnego usług/Windows Desktop**. |
| Źródło danych odnajdywania problemy ze względu na sieć                                            | Użytkownicy potrzebują do kontaktowania się z administratorem sieci, ich. |
| Klientów nawiązujących połączenie                                                                    | Zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md) i jeśli to nie rozwiąże problemu, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md). |
| Czas odpowiedzi aplikacji zdalnej lub pulpitu                                      | Jeśli problemy są powiązane z określoną aplikacją lub produkt, skontaktuj się z zespół odpowiedzialny za danego produktu. |
| Komunikaty licencjonowania lub błędów                                                          | Jeśli problemy są powiązane z określoną aplikacją lub produkt, skontaktuj się z zespół odpowiedzialny za danego produktu. |

## <a name="next-steps"></a>Następne kroki

- Aby rozwiązywać problemy podczas tworzenia puli dzierżawy i hosta w środowisku Windows pulpitu wirtualnego, zobacz [dzierżawy i hostów puli tworzenia](troubleshoot-set-up-issues.md).
- Aby rozwiązywać problemy podczas konfigurowania maszyny wirtualnej (VM) w Windows pulpitu wirtualnego, zobacz [konfigurację maszyny wirtualnej hosta sesji](troubleshoot-vm-configuration.md).
- Aby rozwiązywać problemy z połączeniami klienta pulpitu wirtualnego Windows, zobacz [połączeń klienta usług pulpitu zdalnego](troubleshoot-client-connection.md).
- Aby rozwiązywać problemy podczas korzystania z programu PowerShell z usługą Windows pulpitu wirtualnego, zobacz [środowiska PowerShell pulpitu wirtualnego Windows](troubleshoot-powershell.md).
- Aby dowiedzieć się więcej na temat usługi w wersji zapoznawczej, zobacz [Windows Desktop w wersji zapoznawczej środowiska](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Aby przejść przez samouczek rozwiązywania problemów, zobacz [samouczka: Rozwiązywanie problemów z wdrożeniami szablonu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Aby dowiedzieć się więcej na temat inspekcji akcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Aby dowiedzieć się więcej o akcjach, aby określić błędy podczas wdrażania, zobacz [wyświetlanie operacji wdrażania](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).