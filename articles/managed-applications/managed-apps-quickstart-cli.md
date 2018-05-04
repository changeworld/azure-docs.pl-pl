---
title: Tworzenie aplikacji zarządzanej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure | Microsoft Docs
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 04/13/2018
ms.author: tomfitz
ms.openlocfilehash: 46ea192220ced18b25d60030527d1f76fb37962a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-deploy-an-azure-managed-application-with-azure-cli"></a>Tworzenie i wdrażanie aplikacji zarządzanej platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Ten artykuł zawiera wprowadzenie dotyczące pracy z aplikacjami zarządzanymi. Możesz dodać definicję aplikacji zarządzanej do katalogu wewnętrznego dla użytkowników w organizacji. Następnie możesz wdrożyć tę aplikację zarządzaną do swojej subskrypcji. Aby uprościć wprowadzenie, utworzyliśmy już pliki dla aplikacji zarządzanej. Jeden z plików definiuje infrastrukturę rozwiązania. Drugi plik definiuje interfejs użytkownika służący do wdrażania aplikacji zarządzanej za pośrednictwem portalu. Te pliki są dostępne w serwisie GitHub. Dzięki samouczkowi [Tworzenie aplikacji w katalogu usług](publish-service-catalog-app.md) możesz dowiedzieć się, jak utworzyć te pliki.

Gdy skończysz, będziesz mieć trzy grupy zasobów zawierające różne części aplikacji zarządzanej.

| Grupa zasobów | Contains | Opis |
| -------------- | -------- | ----------- |
| appDefinitionGroup | Definicja aplikacji zarządzanej. | Wydawca tworzy tę grupę zasobów i definicję aplikacji zarządzanej. Dowolna osoba mająca dostęp do definicji aplikacji zarządzanej może ją wdrożyć. |
| applicationGroup | Wystąpienie aplikacji zarządzanej. | Odbiorca tworzy tę grupę zasobów i wystąpienie aplikacji zarządzanej. Odbiorca może aktualizować aplikację zarządzaną za pośrednictwem tego wystąpienia. |
| infrastructureGroup | Zasoby aplikacji zarządzanej. | Ta grupa zasobów jest tworzona automatycznie po utworzeniu aplikacji zarządzanej. Wydawca ma dostęp do tej grupy zasobów i może zarządzać aplikacją. Odbiorca ma ograniczony dostęp do grupy zasobów. |

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Utwórz grupę zasobów dla definicji.

Twoja definicja aplikacji zarządzanej istnieje w grupie zasobów. Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Aby utworzyć grupę zasobów, użyj następującego polecenia:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Tworzenie definicji aplikacji zarządzanej

Podczas tworzenia aplikacji zarządzanej należy wybrać użytkownika, grupę lub aplikację, które zarządzają zasobami w imieniu odbiorcy. Ta tożsamość ma uprawnienia w zarządzanej grupie zasobów zgodne z przypisaną rolą. Zazwyczaj w celu zarządzania zasobami tworzy się grupę usługi Azure Active Directory. Jednak na potrzeby tego artykułu użyj swojej tożsamości.

Aby uzyskać identyfikator obiektu tożsamości, podaj główną nazwę użytkownika w następującym poleceniu:

```azurecli-interactive
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
```

Następnie potrzebny będzie identyfikator definicji roli wbudowanej RBAC, do której chcesz udzielić dostępu użytkownikowi. W następującym poleceniu przedstawiono sposób uzyskiwania identyfikatora definicji dla roli Właściciel:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Teraz utwórz zasób definicji aplikacji zarządzanej. Aplikacja zarządzana zawiera tylko konto magazynu.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://raw.githubusercontent.com/Azure/azure-managedapp-samples/master/samples/201-managed-storage-account/managedstorage.zip"
```

Gdy wykonywanie polecenia zakończy się, będziesz mieć w swojej grupie zasobów definicję aplikacji zarządzanej. 

Niektóre z parametrów użytych w powyższym przykładzie to:

* **resource-group**: nazwa grupy zasobów, w której tworzona jest definicja aplikacji zarządzanej.
* **lock-level**: typ blokady nałożonej na zarządzaną grupę zasobów. Uniemożliwia klientowi wykonywanie niepożądanych operacji na tej grupie zasobów. Obecnie jedynym obsługiwanym poziomem blokady jest ReadOnly. Gdy zostanie określony poziom ReadOnly, klient może jedynie odczytywać zasoby obecne w zarządzanej grupie zasobów. Tożsamości wydawcy, które mają dostęp do zarządzanej grupy zasobów, są wykluczone z blokady.
* **authorizations**: opisuje identyfikator podmiotu zabezpieczeń i identyfikator definicji roli, przy użyciu których udzielany jest dostęp do zarządzanej grupy zasobów. Określa się go w formacie `<principalId>:<roleDefinitionId>`. Dla tej właściwości można także określić wiele wartości. Jeśli potrzebnych jest wiele wartości, można je określić w formacie `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Wartości rozdziela się spacją.
* **package-file-uri**: lokalizacja pakietu zip zawierającego wymagane pliki. Pakiet zawiera co najmniej pliki **mainTemplate.json** i **createUiDefinition.json**. **mainTemplate.json**: określa zasoby platformy Azure, które są aprowizowane w ramach aplikacji zarządzanej. Szablon nie różni się niczym od zwykłego szablonu usługi Resource Manager. **createUiDefinition.json**: generuje interfejs użytkownika dla użytkowników, którzy tworzą aplikację zarządzaną za pośrednictwem portalu.

## <a name="create-resource-group-for-managed-application"></a>Tworzenie grupy zasobów dla aplikacji zarządzanej

Teraz możesz wdrożyć aplikację zarządzaną. 

Aby pakiet zawierał wdrożoną aplikację zarządzaną, potrzebna jest grupa zasobów. Jako lokalizacji użyj **westcentralus**.

```azurecli-interactive
az group create --name applicationGroup --location westcentralus
```

## <a name="deploy-the-managed-application"></a>Wdrażanie aplikacji zarządzanej

Aplikację wdraża się przy użyciu następujących poleceń:

```azurecli-interactive
appid=$(az managedapp definition show --name ManagedStorage --resource-group appDefinitionGroup --query id --output tsv)
subid=$(az account show --query id --output tsv)
managedGroupId=/subscriptions/$subid/resourceGroups/infrastructureGroup

az managedapp create \
  --name storageApp \
  --location "westcentralus" \
  --kind "Servicecatalog" \
  --resource-group applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id $managedGroupId \
  --parameters "{\"storageAccountNamePrefix\": {\"value\": \"storage\"}, \"storageAccountType\": {\"value\": \"Standard_LRS\"}}"
```

Niektóre z parametrów użytych w powyższym przykładzie to:

* **managedapp-definition-id**: identyfikator definicji utworzonej we wcześniejszej części tego artykułu.
* **managed-rg-id**: identyfikator grupy zasobów dla zasobów skojarzonych z aplikacją zarządzaną. Polecenie powoduje utworzenie tej grupy zasobów. **Nie może ona istnieć przed uruchomieniem polecenia**. Tą grupą zasobów zarządza wydawca. 
* **resource-group**: grupa zasobów, w której tworzony jest zasób aplikacji zarządzanej.
* **parameters**: parametry potrzebne dla zasobów skojarzonych z aplikacją zarządzaną.

Po pomyślnym zakończeniu wdrażania aplikacja zarządzana zostanie utworzona w grupie applicationGroup. Zasób storageAccount zostanie utworzony w grupie infrastructureGroup.

## <a name="next-steps"></a>Następne kroki

* Zobacz artykuł [Omówienie aplikacji zarządzanych](overview.md) zawierający wprowadzenie do aplikacji zarządzanych.
* Przykładowe pliki znajdują się na stronie [Przykłady aplikacji zarządzanych](https://github.com/Azure/azure-managedapp-samples/tree/master/samples).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).
