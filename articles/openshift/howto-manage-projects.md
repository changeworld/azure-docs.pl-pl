---
title: Zarządzanie zasobami w usłudze Azure Red Hat OpenShift | Dokumenty firmy Microsoft
description: Zarządzanie projektami, szablonami i strumieniami obrazów w klastrze OpenShift usługi Azure Red Hat
services: openshift
keywords: projekt openshift red hat żąda samoobsługowego
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139117"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Zarządzanie projektami, szablonami i strumieniami obrazów w klastrze OpenShift usługi Azure Red Hat 

W platformie kontenerów OpenShift projekty są używane do grupowania i izolowania powiązanych obiektów. Jako administrator możesz dać deweloperom dostęp do określonych projektów, zezwolić im na tworzenie własnych projektów i przyznawać im prawa administracyjne do poszczególnych projektów.

## <a name="self-provisioning-projects"></a>Projekty samoobsługowe

Można umożliwić deweloperom tworzenie własnych projektów. Punkt końcowy interfejsu API jest odpowiedzialny za inicjowanie obsługi administracyjnej projektu zgodnie z szablonem o nazwie żądanie projektu. Konsola sieci `oc new-project` web i polecenie używają tego punktu końcowego, gdy deweloper tworzy nowy projekt.

Po przesłaniu żądania projektu interfejs API zastępuje następujące parametry w szablonie:

| Parametr               | Opis                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Nazwa projektu. Wymagany.             |
| PROJECT_DISPLAYNAME     | Wyświetlana nazwa projektu. Może być pusty. |
| PROJECT_DESCRIPTION     | Opis projektu. Może być pusty.  |
| PROJECT_ADMIN_USER      | Nazwa użytkownika administrującego.       |
| PROJECT_REQUESTING_USER | Nazwa użytkownika żądającego.           |

Dostęp do interfejsu API jest przyznawany deweloperom z wiązaniem roli klastra samoaprosztowych. Ta funkcja jest domyślnie dostępna dla wszystkich uwierzytelnionych deweloperów.

## <a name="modify-the-template-for-a-new-project"></a>Modyfikowanie szablonu dla nowego projektu 

1. Zaloguj się jako `customer-admin` użytkownik z uprawnieniami.

2. Edytuj domyślny szablon żądania projektu.

   ```
   oc edit template project-request -n openshift
   ```

3. Usuń domyślny szablon projektu z procesu aktualizacji azure red hat openshift (ARO), dodając następującą adnotację:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Szablon żądania projektu nie zostanie zaktualizowany przez proces aktualizacji ARO. Dzięki temu klienci mogą dostosować szablon i zachować te dostosowania po zaktualizowaniu klastra.

## <a name="disable-the-self-provisioning-role"></a>Wyłączanie roli samoobsługowej

Można uniemożliwić uwierzytelnionej grupie użytkowników samodzielne inicjowanie obsługi administracyjnej nowych projektów.

1. Zaloguj się jako `customer-admin` użytkownik z uprawnieniami.

2. Edytuj samoaprowizatory powiązania roli klastra.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Usuń rolę z procesu aktualizacji ARO, dodając następującą adnotację: `openshift.io/reconcile-protect: "true"`.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Zmień powiązanie roli `system:authenticated:oauth` klastra, aby uniemożliwić tworzenie projektów:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Zarządzanie domyślnymi szablonami i strumieniami obrazów

W usłudze Azure Red Hat OpenShift można wyłączyć aktualizacje `openshift` dla wszystkich szablonów domyślnych i strumieni obrazów wewnątrz obszaru nazw.
Aby wyłączyć aktualizacje `ImageStreams` `openshift` dla WSZYSTKICH `Templates` i w obszarze nazw:

1. Zaloguj się jako `customer-admin` użytkownik z uprawnieniami.

2. Edytuj `openshift` obszar nazw:

   ```
   oc edit namespace openshift
   ```

3. Usuń `openshift` obszar nazw z procesu aktualizacji ARO, dodając następującą adnotację:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Każdy pojedynczy obiekt `openshift` w obszarze nazw można usunąć z procesu `openshift.io/reconcile-protect: "true"` aktualizacji, dodając do niego adnotację.

## <a name="next-steps"></a>Następne kroki

Wypróbuj samouczek:
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md)
