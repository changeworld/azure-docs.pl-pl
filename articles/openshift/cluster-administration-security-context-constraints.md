---
title: Zarządzanie ograniczeniami kontekstu zabezpieczeń w usłudze Azure Red Hat OpenShift | Dokumenty firmy Microsoft
description: Ograniczenia kontekstu zabezpieczeń dla administratorów klastrów Azure Red Hat OpenShift
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 24163adcec889e9eedc2362ff1f01f00257a98f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063171"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Zarządzanie ograniczeniami kontekstu zabezpieczeń w usłudze Azure Red Hat OpenShift 

Ograniczenia kontekstu zabezpieczeń (SCC) umożliwiają administratorom klastra kontrolowanie uprawnień zasobników. Aby dowiedzieć się więcej o tym typie interfejsu API, zobacz [dokumentację architektury kontrolerów SC.](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html) Scc w wystąpieniu można zarządzać jako normalne obiekty interfejsu API przy użyciu interfejsu wiersza polecenia.

## <a name="list-security-context-constraints"></a>Wyświetlanie ograniczeń kontekstu zabezpieczeń

Aby uzyskać bieżącą listę kontrolerów SC, użyj tego polecenia: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Sprawdzanie obiektu pod kątem ograniczeń kontekstu zabezpieczeń

Aby zbadać określony SCC, `oc describe`użyj `oc edit` `oc get`, lub .  Na przykład, aby sprawdzić **ograniczone** SCC, użyj tego polecenia:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) 
