---
title: Konfiguracja oparta na STIG do użycia w konfiguracji stanu — Usługa Azure Automation
description: Dowiedz się więcej o konfiguracjach opartych na STIG dla konfiguracji stanu w usłudze Azure Automation.
keywords: dsc,powershell,konfiguracja,konfiguracja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6d257198fcae54b1214d77f6b905d876d2687f5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585527"
---
# <a name="configuration-based-on-stig"></a>Konfiguracja oparta na metodologii STIG

> Dotyczy: Windows PowerShell 5.1

Tworzenie zawartości konfiguracji po raz pierwszy może być trudne.
W wielu przypadkach celem jest zautomatyzowanie konfiguracji serwerów zgodnie z "linią bazową", która, miejmy nadzieję, jest zgodna z zaleceniem branżowym.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy z githubem, a nie od firmy Microsoft.

## <a name="community-project-powerstig"></a>Projekt wspólnotowy: PowerSTIG

Projekt społeczności o nazwie [PowerSTIG](https://github.com/microsoft/powerstig) ma na celu rozwiązanie tego problemu poprzez generowanie zawartości DSC na podstawie [publicznych informacji](https://public.cyber.mil/stigs/) na temat STIG (Security Technical Implementation Guide),

Radzenie sobie z liniami bazowymi jest bardziej skomplikowane niż się wydaje.
Wiele organizacji musi [dokumentować wyjątki od](https://github.com/microsoft/powerstig#powerstigdata) reguł i zarządzać tymi danymi na dużą skalę.
PowerSTIG rozwiązuje ten problem, udostępniając [zasoby złożone w](https://github.com/microsoft/powerstig#powerstigdsc) celu rozwiązania każdego obszaru konfiguracji, zamiast próbować rozwiązać cały zakres ustawień w jednym dużym pliku.

Po wygenerowaniu konfiguracji można użyć [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/configurations) do generowania plików MOF i [przekazywania plików MOF do usługi Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Następnie zarejestruj serwery z [lokalnego](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub [na platformie Azure,](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) aby pobierać konfiguracje.

Aby wypróbować program PowerSTIG, odwiedź [galerię programu PowerShell](https://www.powershellgallery.com) i pobierz rozwiązanie lub kliknij "Witryna projektu", aby wyświetlić [dokumentację.](https://github.com/microsoft/powerstig)

## <a name="next-steps"></a>Następne kroki

- [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaconfig)
