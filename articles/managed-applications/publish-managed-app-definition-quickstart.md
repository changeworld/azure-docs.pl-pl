---
title: Tworzenie definicji aplikacji zarządzanej platformy Azure | Microsoft Docs
description: Przedstawia sposób tworzenia aplikacji zarządzanej platformy Azure przeznaczonej dla członków Twojej organizacji.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1f80d7e63d994f0e3eb3733b99afaa1b056f4686
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60252394"
---
# <a name="publish-an-azure-managed-application-definition"></a>Publikowanie definicji aplikacji zarządzanej platformy Azure

Ten przewodnik Szybki start zawiera wprowadzenie dotyczące pracy z aplikacjami zarządzanymi. Możesz dodać definicję aplikacji zarządzanej do katalogu wewnętrznego dla użytkowników w organizacji. Aby uprościć wprowadzenie, utworzyliśmy już pliki dla aplikacji zarządzanej. Te pliki są dostępne w serwisie GitHub. Dzięki samouczkowi [Tworzenie aplikacji w katalogu usług](publish-service-catalog-app.md) możesz dowiedzieć się, jak utworzyć te pliki.

Po zakończeniu będziesz mieć grupę zasobów o nazwie **appDefinitionGroup**, zawierającą definicję aplikacji zarządzanej.

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

* **Grupa zasobów**: Nazwa grupy zasobów, w której tworzona jest definicja aplikacji zarządzanej.
* **lock-level**: Typ blokady nałożonej na zarządzanej grupy zasobów. Uniemożliwia klientowi wykonywanie niepożądanych operacji na tej grupie zasobów. Obecnie jedynym obsługiwanym poziomem blokady jest ReadOnly. Gdy zostanie określony poziom ReadOnly, klient może jedynie odczytywać zasoby obecne w zarządzanej grupie zasobów. Tożsamości wydawcy, które mają dostęp do zarządzanej grupy zasobów, są wykluczone z blokady.
* **autoryzacje**: Opisuje identyfikator podmiotu zabezpieczeń i identyfikator definicji roli, które są używane do udzielania uprawnień do zarządzanej grupy zasobów. Określa się go w formacie `<principalId>:<roleDefinitionId>`. Jeśli wymagana jest więcej niż jedna wartość, należy podać wartości w formacie `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Wartości rozdziela się spacją.
* **package-file-uri**: Lokalizacja pakietu ZIP zawierającego wymagane pliki. Pakiet musi zawierać pliki **mainTemplate.json** i **createUiDefinition.json**. **mainTemplate.json**: określa zasoby platformy Azure, które są tworzone w ramach aplikacji zarządzanej. Szablon nie różni się niczym od zwykłego szablonu usługi Resource Manager. **createUiDefinition.json**: generuje interfejs użytkownika dla użytkowników, którzy tworzą aplikację zarządzaną za pośrednictwem portalu.

## <a name="next-steps"></a>Kolejne kroki

Opublikowano definicję aplikacji zarządzanej. Teraz możesz dowiedzieć się, jak wdrożyć wystąpienie tej definicji.

> [!div class="nextstepaction"]
> [Szybki start: Wdróż aplikację katalogu usług](deploy-service-catalog-quickstart.md)