---
title: Konwertowanie konfiguracji na zasoby złożone na potrzeby konfiguracji stanu — Azure Automation
description: Dowiedz się, jak konwertować konfiguracje na zasoby złożone, aby skonfigurować stan w Azure Automation.
keywords: DSC, PowerShell, konfiguracja, instalacja
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9e76532d41e23cba376755ca524ca6911385204
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559499"
---
# <a name="convert-configurations-to-composite-resources"></a>Konwertuj konfiguracje na zasoby złożone

> Dotyczy: Windows PowerShell 5.1

Po rozpoczęciu tworzenia konfiguracji można szybko utworzyć "scenariusze" służące do zarządzania grupami ustawień.
Oto przykłady:

- Tworzenie serwera sieci Web
- Tworzenie serwera DNS
- Tworzenie serwera programu SharePoint
- Konfigurowanie klastra SQL
- Zarządzanie ustawieniami zapory
- Zarządzanie ustawieniami hasła

Jeśli interesuje Cię udostępnianie tej pracy innym osobom, najlepszym rozwiązaniem jest spakowanie konfiguracji jako [zasobu złożonego](/powershell/dsc/resources/authoringresourcecomposite).
Tworzenie zasobów złożonych po raz pierwszy może być przeciążone.

> [!NOTE]
> Ten artykuł odnosi się do rozwiązania, które jest obsługiwane przez społeczność typu open source.
> Pomoc techniczna jest dostępna tylko w formie współpracy GitHub, a nie od firmy Microsoft.

## <a name="community-project-compositeresource"></a>Projekt społeczności: CompositeResource

Zostało utworzone rozwiązanie obsługiwane przez społeczność o nazwie [CompositeResource](https://github.com/microsoft/compositeresource) , aby rozwiązać ten problem.

CompositeResource automatyzuje proces tworzenia nowego modułu na podstawie konfiguracji.
Rozpoczynając od [kropki](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) pozyskuje skrypt konfiguracji na stacji roboczej (lub serwerze kompilacji), tak aby został załadowany w pamięci.
Następnie zamiast uruchamiać konfigurację w celu wygenerowania pliku MOF, użyj funkcji dostarczonej przez moduł CompositeResource w celu zautomatyzowania konwersji.
Polecenie cmdlet spowoduje załadowanie zawartości konfiguracji, uzyskanie listy parametrów i wygenerowanie nowego modułu ze wszystkimi potrzebnymi elementami.

Po wygenerowaniu modułu można zwiększyć wersję i dodać informacje o wersji za każdym razem, gdy wprowadzisz zmiany i opublikujesz je w twoim własnym [repozytorium PowerShellGet](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

Po utworzeniu złożonego modułu zasobów z konfiguracją (lub wieloma konfiguracjami) można użyć ich w ramach [tworzenia](/azure/automation/compose-configurationwithcompositeresources) i dodawania do [skryptów konfiguracji DSC](/powershell/dsc/resources/configurations) w celu wygenerowania plików MOF i [Przekaż pliki MOF do Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Następnie zarejestruj serwery [lokalnie](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) lub na [platformie Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) , aby przeprowadzić konfigurację ściągania.
Najnowsza aktualizacja projektu opublikowała również [elementy Runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) dla Azure Automation w celu zautomatyzowania procesu importowania konfiguracji z Galeria programu PowerShell.

Aby wypróbować automatyzację tworzenia zasobów złożonych dla DSC, odwiedź [Galeria programu PowerShell](https://www.powershellgallery.com/packages/compositeresource/) i Pobierz rozwiązanie lub kliknij pozycję "Witryna projektu", aby wyświetlić [dokumentację](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Następne kroki

- [Przegląd konfiguracji żądanego stanu programu Windows PowerShell](/powershell/dsc/overview/overview)
- [Zasoby DSC](/powershell/dsc/resources/resources)
- [Konfigurowanie Configuration Manager lokalnego](/powershell/dsc/managing-nodes/metaconfig)
