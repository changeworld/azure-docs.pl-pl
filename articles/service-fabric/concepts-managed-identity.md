---
title: Service Fabric Omówienie tożsamości zarządzanej | Microsoft Docs
description: Ten artykuł zawiera omówienie tożsamości zarządzanej.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: edce98e6babb676ee72f1d254b929e557332dd75
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333124"
---
# <a name="managed-identity-for-service-fabric-application-preview"></a>Tożsamość zarządzana dla aplikacji Service Fabric (wersja zapoznawcza)

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Utrzymywanie bezpiecznych poświadczeń jest ważnym zadaniem, ponieważ nigdy nie pojawiają się na stacjach roboczych deweloperów i nie są zaewidencjonowane do kontroli źródła. Funkcja zarządzanej tożsamości dla zasobów platformy Azure w Azure Active Directory (Azure AD) rozwiązuje ten problem. Ta funkcja udostępnia usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie.

Funkcja tożsamości zarządzanej dla zasobów platformy Azure jest bezpłatna za pomocą usługi Azure AD dla subskrypcji platformy Azure. Nie ma dodatkowych kosztów.

> [!NOTE]
> Tożsamość zarządzana dla zasobów platformy Azure to nowa nazwa usługi znana wcześniej jako tożsamość usługi zarządzanej (MSI).

## <a name="terminology"></a>Terminologia

Poniższe terminy są używane w całej zarządzanej tożsamości dla zestawu dokumentacji zasobów platformy Azure:

- **Identyfikator klienta** — unikatowy identyfikator wygenerowany przez usługę Azure AD, który jest powiązany z aplikacją i jednostką usługi podczas początkowej aprowizacji (Zobacz również [Identyfikator aplikacji](/azure/active-directory/develop/developer-glossary#application-id-client-id)).

- **Identyfikator podmiotu zabezpieczeń** — identyfikator obiektu jednostki usługi dla tożsamości zarządzanej, który jest używany do udzielania dostępu opartego na rolach do zasobu platformy Azure.

- Nazwa **główna usługi** — obiekt Azure Active Directory, który reprezentuje projekcję aplikacji usługi AAD w danej dzierżawie (Zobacz też [nazwę główną usługi](../active-directory/develop/developer-glossary.md#service-principal-object)).


## <a name="about-managed-identities-in-azure"></a>Informacje o zarządzanych tożsamościach na platformie Azure

- [Typy tożsamości zarządzanej (MI) na platformie Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-does-the-managed-identities-for-azure-resources-work)

- [Jak działa tożsamość zarządzana przypisana przez system na platformie Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-system-assigned-managed-identity-works-with-an-azure-vm)

- [Jak działa tożsamość zarządzana zdefiniowana przez użytkownika (MI) na platformie Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-a-user-assigned-managed-identity-works-with-an-azure-vm)


## <a name="supported-scenarios-for-service-fabric-applications"></a>Obsługiwane scenariusze dla aplikacji Service Fabric

Zarządzane tożsamości dla Service Fabric są obsługiwane tylko w klastrach Service Fabric wdrożonych na platformie Azure i tylko w przypadku aplikacji wdrożonych jako zasoby platformy Azure; nie można przypisać tożsamości do aplikacji, które nie zostały wdrożone jako zasoby platformy Azure. Koncepcyjnie mówiąc, obsługa zarządzanych tożsamości w klastrze Service Fabric platformy Azure składa się z dwóch faz:

1. Przypisz co najmniej jedną zarządzaną tożsamość do zasobu aplikacji; do aplikacji można przypisać jedną tożsamość przypisaną do systemu i/lub do 32 tożsamości przypisanych do użytkownika.

2. W definicji aplikacji zamapuj jedną z tożsamości przypisanych do aplikacji do dowolnej usługi obejmującej aplikację.

Tożsamość przypisana przez system aplikacji jest unikatowa dla tej aplikacji; tożsamość przypisana przez użytkownika to zasób autonomiczny, który może być przypisany do wielu aplikacji. W ramach aplikacji można przypisać jedną tożsamość (którą przypisano do systemu lub przypisanej do użytkownika) do wielu usług aplikacji, ale do każdej usługi można przypisać tylko jedną tożsamość. Wreszcie do usługi należy przypisać tożsamość jawnie, aby mieć dostęp do tej funkcji. W efekcie mapowanie tożsamości aplikacji do jej usług składowych pozwala na izolację w aplikacji — usługa może korzystać tylko z zamapowanej tożsamości (a nie w ogóle, jeśli nie została jawnie przypisana).  

Lista obsługiwanych scenariuszy dotyczących wersji zapoznawczej jest następująca:

   - Wdróż nową aplikację przy użyciu co najmniej jednej usługi i co najmniej jednej przypisanej tożsamości

   - Przypisz co najmniej jedną zarządzaną tożsamość do istniejącej aplikacji w celu uzyskania dostępu do zasobów platformy Azure. Aplikacja musi zostać wdrożona jako zasób platformy Azure


Następujące scenariusze nie są obsługiwane lub nie są zalecane; Uwaga te akcje mogą nie być blokowane, ale mogą powodować awarię aplikacji:

   - Usuwanie lub zmiana tożsamości przypisanych do aplikacji; Jeśli musisz wprowadzić zmiany, Prześlij oddzielne wdrożenia, aby najpierw dodać nowe przypisanie tożsamości, a następnie usunąć wcześniej przypisane. Usunięcie tożsamości z istniejącej aplikacji może mieć niepożądane skutki, w tym pozostawienie aplikacji w stanie, który nie jest uaktualniony. Całkowicie można bezpiecznie usunąć aplikację, jeśli konieczne jest usunięcie tożsamości; Uwaga: spowoduje to usunięcie tożsamości przypisanej do systemu (jeśli została zdefiniowana) skojarzonej z aplikacją i spowoduje usunięcie wszelkich skojarzeń z tożsamościami przypisanymi do aplikacji.

   - Obsługa tożsamości zarządzanych nie jest teraz zintegrowana z [AzureServiceTokenProvider](../key-vault/service-to-service-authentication.md); Integracja zostanie osiągnięta do końca okresu zapoznawczego dla funkcji zarządzanej tożsamości.

>
> [!NOTE]
>
> Ta funkcja jest dostępna w wersji zapoznawczej; w związku z tym może podlegać częstym zmianom i mogą nie być odpowiednie dla wdrożeń produkcyjnych.

## <a name="next-steps"></a>Następne kroki
* [Wdróż nowy klaster Service Fabric platformy Azure z obsługą tożsamości zarządzanej](./configure-new-azure-service-fabric-enable-managed-identity.md) 
* [Włączanie obsługi tożsamości zarządzanych w istniejącym klastrze Service Fabric platformy Azure](./configure-existing-cluster-enable-managed-identity-token-service.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej do systemu](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Wdrażanie aplikacji Service Fabric platformy Azure przy użyciu tożsamości zarządzanej przypisanej przez użytkownika](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Korzystanie z zarządzanej tożsamości aplikacji Service Fabric z poziomu kodu usługi](./how-to-managed-identity-service-fabric-app-code.md)
* [Przyznaj aplikacji Service Fabric platformy Azure dostęp do innych zasobów platformy Azure](./how-to-grant-access-other-resources.md)
