---
title: Uruchamianie kontenerów uprzywilejowanych w klastrze OpenShift usługi Azure Red Hat | Dokumenty firmy Microsoft
description: Uruchom kontenery uprzywilejowane, aby monitorować bezpieczeństwo i zgodność.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, czerwony kapelusz
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271379"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Uruchamianie uprzywilejowanych kontenerów w klastrze usługi Azure Red Hat OpenShift

Nie można uruchomić dowolnych kontenerów uprzywilejowanych w klastrach OpenShift usługi Azure Red Hat.
Dwa rozwiązania do monitorowania zabezpieczeń i zgodności mogą być uruchamiane w klastrach ARO.
W tym dokumencie opisano różnice w stosunku do ogólnej dokumentacji wdrażania programu OpenShift dostawców produktów zabezpieczających.


Zapoznaj się z tymi instrukcjami przed postępem w instrukcji dostawcy.
Tytuły sekcji w poniższych krokach dotyczących produktu odnoszą się bezpośrednio do tytułów sekcji w dokumentacji dostawców.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Dokumentacja większości produktów zabezpieczeń zakłada, że masz uprawnienia administratora klastra.
Administratorzy klientów nie mają wszystkich uprawnień w usłudze Azure Red Hat OpenShift. Uprawnienia wymagane do modyfikowania zasobów w całym klastrze są ograniczone.

Najpierw upewnij się, że użytkownik jest zalogowany do klastra jako administrator klienta, uruchamiając `oc get scc`program . Wszyscy użytkownicy, którzy są członkami grupy administratorów klienta, mają uprawnienia do wyświetlania ograniczeń kontekstu zabezpieczeń (SCC) w klastrze.

Następnie upewnij się, że wersja binarna `oc` jest `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Kroki specyficzne dla produktu dla Aqua Security
Podstawowe instrukcje, które zostaną zmodyfikowane, można znaleźć w [dokumentacji wdrażania Aqua Security.](https://docs.aquasec.com/docs/openshift-red-hat) Kroki w tym miejscu będą uruchamiane w połączeniu z dokumentacją wdrożenia Aqua.

Pierwszym krokiem jest dodawanie adnotacji do wymaganych kontrolerów SPC, które zostaną zaktualizowane. Adnotacje te uniemożliwiają klastra Sync Pod z powrotem żadnych zmian do tych SSC.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Krok 1: Przygotowanie wymagań wstępnych
Pamiętaj, aby zalogować się do klastra jako administrator klienta ARO zamiast roli administratora klastra.

Utwórz projekt i konto usługi.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Zamiast przypisywać rolę czytnika klastra, przypisz rolę klient-administrator-klaster do konta aqua za pomocą następującego polecenia.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Kontynuuj przestrzeganie pozostałych instrukcji w kroku 1.  Instrukcje te opisują utworzenie tajemnicy rejestru Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Krok 2: Wdrażanie serwera Aqua, bazy danych i bramy
Postępuj zgodnie z instrukcjami podanymi w dokumentacji Aqua do zainstalowania aqua-console.yaml.

Zmodyfikuj dostarczony `aqua-console.yaml`plik .  Usuń dwa pierwsze obiekty `kind: ClusterRole` oznaczone `kind: ClusterRoleBinding`etykietą i .  Te zasoby nie zostaną utworzone, ponieważ administrator klienta nie ma `ClusterRole` obecnie `ClusterRoleBinding` uprawnień do modyfikowania i administracji obiektów.

Druga modyfikacja będzie `kind: Route` do części `aqua-console.yaml`. Zastąp następujący `kind: Route` yaml `aqua-console.yaml` dla obiektu w pliku.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Postępuj zgodnie z pozostałymi instrukcjami.

### <a name="step-3-login-to-the-aqua-server"></a>Krok 3: Zaloguj się do serwera Aqua
Ta sekcja nie jest w żaden sposób modyfikowana.  Postępuj zgodnie z dokumentacją Aqua.

Użyj następującego polecenia, aby uzyskać adres Aqua Console.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Krok 4: Wdrażanie Aqua Enforcers
Podczas wdrażania wymuszaczy ustaw następujące pola:

| Pole          | Wartość         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| Serviceaccount | aqua-konto  |
| Project        | aqua-bezpieczeństwo |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Kroki specyficzne dla produktu Prisma Cloud / Twistlock

Podstawowe instrukcje, które zamierzamy zmodyfikować, można znaleźć w [dokumentacji wdrażania prisma cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Zacznij od `twistcli` zainstalowania narzędzia zgodnie z opisem w sekcjach "Zainstaluj Prisma Cloud" i "Pobierz oprogramowanie Prisma Cloud".

Tworzenie nowego projektu OpenShift
```
oc new-project twistlock
```

Pomiń opcjonalną sekcję "Wypychanie obrazów Prisma Cloud do rejestru prywatnego". Nie będzie działać na azure Red Hat Openshift. Zamiast tego użyj rejestru online.

Możesz postępować zgodnie z oficjalną dokumentacją, stosując poprawki opisane poniżej.
Zacznij od sekcji "Zainstaluj konsolę".

### <a name="install-console"></a>Zainstaluj konsolę

Podczas `oc create -f twistlock_console.yaml` w kroku 2 otrzymasz błąd podczas tworzenia obszaru nazw.
Można go bezpiecznie zignorować, obszar nazw został `oc new-project` utworzony wcześniej za pomocą polecenia.

Służy `azure-disk` do przechowywania typu.

### <a name="create-an-external-route-to-console"></a>Tworzenie trasy zewnętrznej do konsoli

Możesz postępować zgodnie z dokumentacją lub poniższymi instrukcjami, jeśli wolisz polecenie oc.
Skopiuj następującą definicję trasy do pliku o nazwie twistlock_route.yaml na komputerze
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
następnie uruchom:
```
oc create -f twistlock_route.yaml
```

Za pomocą tego polecenia możesz uzyskać adres URL przypisany do konsoli Twistlock:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfigurowanie konsoli

Postępuj zgodnie z dokumentacją Twistlock.

### <a name="install-defender"></a>Zainstaluj defendera

Podczas `oc create -f defender.yaml` w kroku 2 otrzymasz błędy podczas tworzenia roli klastra i powiązania roli klastra.
Można je zignorować.

Obrońcy będą wdrażane tylko w węzłach obliczeniowych. Nie musisz ograniczać ich za pomocą selektora węzłów.
