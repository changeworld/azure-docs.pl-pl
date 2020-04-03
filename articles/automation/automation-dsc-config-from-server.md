---
title: Tworzenie konfiguracji z istniejących serwerów — usługa Azure Automation
description: Dowiedz się, jak tworzyć konfiguracje z istniejących serwerów usługi Azure Automation.
keywords: dsc,powershell,konfiguracja,konfiguracja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585546"
---
# <a name="create-configurations-from-existing-servers"></a>Tworzenie konfiguracji z istniejących serwerów

> Dotyczy: Windows PowerShell 5.1

Tworzenie konfiguracji z istniejących serwerów może być trudnym zadaniem.
Możesz nie chcieć *wszystkich* ustawień, tylko tych, na których Ci zależy.
Nawet wtedy musisz wiedzieć, w jakiej kolejności ustawienia muszą być stosowane, aby konfiguracja została pomyślnie zastosowana.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy z githubem, a nie od firmy Microsoft.

## <a name="community-project-reversedsc"></a>Projekt wspólnotowy: ReverseDSC

Rozwiązań obsługiwanych przez społeczność o nazwie [ReverseDSC](https://github.com/microsoft/reversedsc) został stworzony do pracy w tym obszarze, począwszy od programu SharePoint.

Rozwiązanie opiera się na [zasobie programu SharePointDSC](https://github.com/powershell/sharepointdsc) i rozszerza je na organizowanie [zbierania informacji](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) z istniejących serwerów programu SharePoint.
Najnowsza wersja ma wiele [trybów ekstrakcji,](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) aby określić, jaki poziom informacji do uwzględnienia.

Wynikiem użycia rozwiązania jest generowanie [danych konfiguracyjnych](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) do użycia ze skryptami konfiguracyjnymi programu SharePointDSC.

Po wygenerowaniu plików danych można ich używać za pomocą [skryptów konfiguracji DSC](/powershell/scripting/dsc/overview/overview) do generowania plików MOF i [przekazywania plików MOF do usługi Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Następnie zarejestruj serwery z [lokalnego](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub [na platformie Azure,](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) aby pobierać konfiguracje.

Aby wypróbować reverseDSC, odwiedź [Galerię programu PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) i pobierz rozwiązanie lub kliknij "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaconfig)
