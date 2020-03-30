---
title: Konwertowanie konfiguracji na zasoby złożone dla konfiguracji stanu — usługa Azure Automation
description: Dowiedz się, jak konwertować konfiguracje na zasoby złożone dla konfiguracji stanu w usłudze Azure Automation.
keywords: dsc,powershell,konfiguracja,konfiguracja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136659"
---
# <a name="convert-configurations-to-composite-resources"></a>Konwertowanie konfiguracji do zasobów złożonych

> Dotyczy: Windows PowerShell 5.1

Po rozpoczęciu tworzenia konfiguracji można szybko utworzyć "scenariusze", które zarządzają grupami ustawień.
Przykładami mogą być:

- tworzenie serwera sieci web
- tworzenie serwera DNS
- tworzenie serwera programu SharePoint
- konfigurowanie klastra SQL
- zarządzanie ustawieniami zapory
- zarządzanie ustawieniami hasła

Jeśli chcesz udostępnić tę pracę innym, najlepszym rozwiązaniem jest spakowanie konfiguracji jako [zasobu złożonego](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Tworzenie zasobów złożonych po raz pierwszy może być przytłaczające.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy z githubem, a nie od firmy Microsoft.

## <a name="community-project-compositeresource"></a>Projekt społecznościowy: CompositeResource

Aby rozwiązać ten problem, utworzono rozwiązanie obsługiwane przez społeczność o nazwie [CompositeResource.](https://github.com/microsoft/compositeresource)

CompositeResource automatyzuje proces tworzenia nowego modułu z konfiguracji.
Zacznij od [dot sourcing](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) skryptu konfiguracji na stacji roboczej (lub serwera kompilacji), więc jest ładowany do pamięci.
Następnie zamiast uruchamiania konfiguracji do generowania pliku MOF, należy użyć funkcji dostarczonej przez moduł CompositeResource do automatyzacji konwersji.
Polecenie cmdlet załaduje zawartość konfiguracji, pobierz listę parametrów i wygeneruje nowy moduł ze wszystkim, czego potrzebujesz.

Po wygenerowaniu modułu można zwiększyć wersję i dodać informacje o wersji za każdym razem, gdy wprowadzać zmiany i publikować je we własnym [repozytorium programu PowerShellGet.](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)

Po utworzeniu modułu zasobów złożonych zawierającego konfigurację (lub wiele konfiguracji) można ich użyć w [programie Createposable Authoring Experience](/azure/automation/compose-configurationwithcompositeresources) na platformie Azure lub dodać je do [skryptów konfiguracji DSC](/powershell/scripting/dsc/configurations/configurations) w celu wygenerowania plików MOF i [przekazania plików MOF do usługi Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery z [lokalnego](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) lub [na platformie Azure,](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) aby pobierać konfiguracje.
Najnowsza aktualizacja projektu opublikowała również [elementy runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) dla usługi Azure Automation w celu zautomatyzowania procesu importowania konfiguracji z galerii programu PowerShell.

Aby wypróbować automatyzację tworzenia zasobów złożonych dla DSC, odwiedź [Galerię programu PowerShell](https://www.powershellgallery.com/packages/compositeresource/) i pobierz rozwiązanie lub kliknij "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Następne kroki

- [Omówienie konfiguracji żądanego stanu programu Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Zasoby DSC](/powershell/scripting/dsc/resources/resources)
- [Konfigurowanie lokalnego menedżera konfiguracji](/powershell/scripting/dsc/managing-nodes/metaconfig)
