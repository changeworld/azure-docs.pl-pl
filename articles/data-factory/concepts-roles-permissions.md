---
title: Role i uprawnienia dla usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: W tym artykule opisano role i uprawnienia wymagane do utworzenia fabryki danych i do pracy z zasobami podrzędnymi.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 10-04/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: cd6e28298b773f466ea317b7217af7709802aa12
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48817516"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role i uprawnienia dla usługi Azure Data Factory

W tym artykule opisano role wymagane do tworzenia i zarządzania zasobami usługi Azure Data Factory oraz uprawnienia nadane przez tych ról.

## <a name="roles-and-requirements"></a>Wymagania i ról

Aby utworzyć wystąpienia usługi Data Factory, konto użytkownika używane do logowania się na platformie Azure musi być członkiem roli *współautora* lub *właściciela* albo *administratorem* subskrypcji platformy Azure. Aby wyświetlić uprawnienia, które masz w subskrypcji, w witrynie Azure portal wybierz swoją nazwę użytkownika w prawym górnym rogu, a następnie wybierz **uprawnienia**. Jeśli masz dostęp do wielu subskrypcji, wybierz odpowiednią subskrypcję. 

Tworzenie i zarządzanie nimi zasoby podrzędne dla fabryki danych — w tym zestawy danych, połączonych usług, potoki, wyzwalaczy i środowiska integration Runtime — poniższe wymagania dotyczą:
- Aby utworzyć i zarządzać zasobami podrzędnymi w witrynie Azure portal, trzeba należeć do **Współautor Data Factory** roli na poziomie grupy zasobów lub nowszej.
- Tworzenie i zarządzanie zasobami podrzędnych za pomocą programu PowerShell lub zestawu SDK, **Współautor** roli na poziomie zasobów lub nowszego jest wystarczająca.

Aby uzyskać przykładowe instrukcje dotyczące sposobu dodawania użytkownika do roli, zobacz [Dodawanie ról](../billing/billing-add-change-azure-subscription-administrator.md) artykułu.

## <a name="set-up-permissions"></a>Ustawianie uprawnień

Po utworzeniu fabryki danych, możesz umożliwić innym użytkownikom pracy z usługą data factory. Aby udzielić dostępu do innych użytkowników, należy dodać je do wbudowanej **Współautor Data Factory** roli w grupie zasobów, który zawiera usługi data factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Zakres roli współautora usługi Data Factory

Członkostwo w grupie **Współautor Data Factory** rola pozwala użytkownikom na wykonywanie następujących czynności:
- Tworzenie, edytowanie i usuwanie fabryki danych i zasobów podrzędnych, w tym zestawy danych, połączonych usług, potoki, wyzwalaczy i środowiska integration Runtime.
- Wdrażanie szablonów usługi Resource Manager. Wdrożenia usługi Resource Manager to metoda wdrażania używane przez fabrykę danych w witrynie Azure portal.
- Zarządzanie alertami usługi App Insights dla usługi data factory.
- Tworzyć bilety pomocy technicznej.

Aby uzyskać więcej informacji na temat tej roli, zobacz [roli współautora usługi Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Wdrażanie szablonu usługi Resource Manager

**Współautor Data Factory** roli na poziomie grupy zasobów lub nowszej, pozwala użytkownikom na wdrażanie szablonów usługi Resource Manager. W wyniku członkowie roli można użyć szablonów usługi Resource Manager do wdrożenia zarówno fabryki danych i ich zasoby podrzędne, w tym zestawy danych, połączonych usług, potoki, wyzwalaczy i środowiska integration Runtime. Członkostwo w tej roli nie zezwala użytkownikom na tworzenie innych zasobów, jednak.

### <a name="custom-scenarios-and-custom-roles"></a>Niestandardowe scenariusze i ról niestandardowych

Czasami może być konieczne przyznanie różne poziomy dostępu dla użytkowników fabryki danych. Na przykład:
- Grupa, której użytkownicy mają uprawnienia tylko w fabryce danych specyficznych dla może być konieczne.
- Lub może być konieczne grupą, gdzie użytkownicy mogą monitorować tylko usługi data factory (lub fabryk), ale nie można go modyfikować.

Scenariusze te niestandardowe można osiągnąć przez tworzenie ról niestandardowych i przypisywania użytkowników do tych ról. Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [niestandardowych ról na platformie Azure](..//role-based-access-control/custom-roles.md).

Poniżej przedstawiono kilka przykładów, które pokazują, co można osiągnąć za pomocą ról niestandardowych:

- Umożliwia użytkownikowi, tworzyć, edytować lub usunąć wszystkie fabryki danych w grupie zasobów w witrynie Azure portal.

  Przypisanie wbudowanej **współautora usługi Data Factory** roli na poziomie grupy zasobów dla użytkownika. Jeśli chcesz zezwolić na dostęp do dowolnej usługi fabryka danych w ramach subskrypcji, należy przypisać rolę na poziomie subskrypcji.

- Pozwól widok użytkownika (odczyt) i monitorować fabrykę danych, ale nie edytować lub go zmienić.

  Przypisanie wbudowanej **czytnika** roli na zasób fabryki danych dla użytkownika.

- Umożliwia użytkownikowi Edytuj fabrykę danych w witrynie Azure portal.

  Ten scenariusz wymaga dwóch przypisań ról.

  1. Przypisanie wbudowanej **Współautor** roli na poziomie usługi data factory.
  2. Utworzyć niestandardową rolę z uprawnieniami do * Microsoft.Resources/deployments/**. Przypisz tę rolę niestandardową do użytkownika na poziomie grupy zasobów.

- Umożliwia użytkownikowi zaktualizować fabrykę danych z programu PowerShell lub zestawu SDK, ale nie w witrynie Azure portal.

  Przypisanie wbudowanej **Współautor** roli na zasób fabryki danych dla użytkownika. Ta rola informuje użytkownika, zapoznaj się z zasobami w witrynie Azure portal, ale użytkownik nie może uzyskać dostępu **Publikuj** i **Opublikuj wszystkie** przycisków.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o rolach na platformie Azure — [poznać definicje ról](../role-based-access-control/role-definitions.md)

- Dowiedz się więcej o **współautora usługi Data Factory** roli - [roli współautora usługi Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).