---
title: Konfiguracja oparta na STIG do użycia w konfiguracji stanu — Azure Automation
description: Więcej informacji na temat konfiguracji w oparciu o konfigurację stanu STIG w programie Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243526"
---
# <a name="configuration-based-on-stig"></a>Konfiguracja oparta na metodologii STIG

> Dotyczy: Windows PowerShell 5,1

Tworzenie zawartości konfiguracji po raz pierwszy może być trudne.
W wielu przypadkach celem jest zautomatyzowanie konfiguracji serwerów po "linii bazowej", które miejmy nadzieję wyrównany do rekomendacji branżowej.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-powerstig"></a>Projekt społeczności: PowerSTIG

Projekt społeczności o nazwie [PowerSTIG](https://github.com/microsoft/powerstig) ma na celu rozwiązanie tego problemu przez wygenerowanie zawartości DSC na podstawie [informacji publicznych](https://public.cyber.mil/stigs/) dostarczonych na temat Stig (Przewodnik po implementacji technicznej zabezpieczeń).

Zajmowanie się liniami bazowymi jest bardziej skomplikowane niż dźwięki IT.
Wiele organizacji musi [udokumentować wyjątki od](https://github.com/microsoft/powerstig#powerstigdata) zasad i zarządzać tymi danymi na dużą skalę.
PowerSTIG problem, dostarczając [zasoby złożone](https://github.com/microsoft/powerstig#powerstigdsc) , aby zająć się każdym obszarem konfiguracji, zamiast próbować rozwiązać cały zakres ustawień w jednym dużym pliku.

Po wygenerowaniu konfiguracji można użyć [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/configurations) do wygenerowania plików MOF i [przekazania plików MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) lub na [platformie Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) , aby przeprowadzić konfigurację ściągania.

Aby wypróbować PowerSTIG, odwiedź [Galeria programu PowerShell](http://www.powershellgallery.com) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Następne kroki

- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/dsc/overview/overview)
- [Zasoby DSC](/powershell/dsc/resources/resources)
- [Konfigurowanie Configuration Manager lokalnego](/powershell/dsc/managing-nodes/metaconfig)
