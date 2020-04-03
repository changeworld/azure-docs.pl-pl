---
title: Dane konfiguracyjne na dużą skalę — usługa Azure Automation
description: Dowiedz się, jak skonfigurować dane na dużą skalę dla konfiguracji stanu w usłudze Azure Automation.
keywords: dsc,powershell,konfiguracja,konfiguracja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585573"
---
# <a name="configuration-data-at-scale"></a>Dane konfiguracji w dużej skali

> Dotyczy: Windows PowerShell 5.1

Zarządzanie setkami lub tysiącami serwerów może być wyzwaniem.
Klienci przekazali informację zwrotną, że najtrudniejszym aspektem jest zarządzanie [danymi konfiguracyjnymi.](/powershell/scripting/dsc/configurations/configdata)
Organizowanie informacji w konstrukcjach logicznych, takich jak lokalizacja, typ i środowisko.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy z githubem, a nie od firmy Microsoft.

## <a name="community-project-datum"></a>Projekt wspólnotowy: Datum

Aby rozwiązać ten problem, stworzono rozwiązanie obsługiwane przez społeczność o nazwie [Bazy pomocy.](https://github.com/gaelcolas/Datum)
Datum opiera się na świetnych pomysłach z innych platform zarządzania konfiguracją i implementuje ten sam typ rozwiązania dla programu PowerShell DSC.
Informacje [są zorganizowane w plikach tekstowych](https://github.com/gaelcolas/Datum#3-intended-usage) na podstawie logicznych pomysłów.
Przykładami mogą być:

- Ustawienia, które powinny być stosowane globalnie
- Ustawienia, które powinny mieć zastosowanie do wszystkich serwerów w lokalizacji
- Ustawienia, które powinny mieć zastosowanie do wszystkich serwerów bazy danych
- Indywidualne ustawienia serwera

Te informacje są uporządkowane w preferowanym formacie pliku (JSON, Yaml lub PSD1).
Następnie polecenia cmdlet są dostarczane do generowania plików danych konfiguracyjnych przez [konsolidację informacji](https://github.com/gaelcolas/Datum#datum-tree) z każdego pliku do pojedynczego widoku roli serwera lub serwera.

Po wygenerowaniu plików danych można ich używać za pomocą [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) do generowania plików MOF i [przekazywania plików MOF do usługi Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Następnie zarejestruj serwery z [lokalnego](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub [na platformie Azure,](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) aby pobierać konfiguracje.

Aby wypróbować datum, odwiedź [Galerię programu PowerShell](https://www.powershellgallery.com/packages/datum/) i pobierz rozwiązanie lub kliknij "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaconfig)
