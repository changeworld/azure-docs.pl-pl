---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: f55bdc774437d280db51fb69f060bea7dc579a26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250604"
---
## <a name="set-up-your-project"></a>konfigurowanie projektu

Czy chcesz zamiast tego Pobierz ten przykładowy projekt programu Android Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip), a następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu, zanim ją wykonać.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu

1. Otwórz program Android Studio, a następnie wybierz **pliku** > **New** > **nowy projekt**.
2. Nadaj nazwę aplikacji, a następnie wybierz **dalej**.
3. Wybierz **21 interfejsu API lub nowszej (Android 5.0)**, a następnie wybierz pozycję **dalej**.
4. Pozostaw **puste działanie** i wybierz **dalej**, a następnie wybierz pozycję **Zakończ**.

### <a name="add-msal-to-your-project"></a>Dodawanie biblioteki MSAL do projektu

1. W programie Android Studio wybierz **skryptów narzędzia Gradle** > **build.gradle (moduł: aplikacja)**.
2. W obszarze **zależności**, wklej następujący kod:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>O tym pakiecie

W poprzednim kodzie pakiet Microsoft Authentication Library (MSAL), która obsługuje wszystkie operacje tokenu, w tym pobieranie, buforowanie, odświeżanie i usuwanie. Dostęp do interfejsów API chroniony przez platforma tożsamości firmy Microsoft, potrzebne są tokeny.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Tworzenie aplikacji interfejsu użytkownika

1. Przejdź do **res** > **układ**, a następnie otwórz **activity_main.xml**.
2. Zmienianie układu działania z `android.support.constraint.ConstraintLayout` lub innych do `LinearLayout`.
3. Dodaj `android:orientation="vertical"` właściwość `LinearLayout` węzła.
4. Wklej następujący kod do `LinearLayout` węzła, zastępując bieżącej zawartości:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
