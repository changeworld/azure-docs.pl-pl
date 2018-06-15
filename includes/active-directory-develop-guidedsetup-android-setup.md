---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0ed42e4ace17db1e681152589cc46d82c26dddff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202683"
---
## <a name="set-up-your-project"></a>Konfigurowanie projektu

Czy chcesz zamiast tego Pobierz ten przykładowy projekt programu Android Studio? [Pobieranie projektu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)i przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowy kod, przed jego wykonania.

### <a name="create-a-new-project"></a>Tworzenie nowego projektu 
1.  Otwórz program Android Studio, a następnie wybierz **pliku** > **nowy** > **nowy projekt**.
2.  Nazwa aplikacji, a następnie wybierz **dalej**.
3.  Wybierz **21 interfejsu API lub nowszej (Android 5.0)**, a następnie wybierz **dalej**.
4.  Pozostaw **pusty działania** jest, wybierz **dalej**, a następnie wybierz **Zakończ**.


### <a name="add-msal-to-your-project"></a>Dodaj MSAL do projektu
1.  W programie Android Studio, wybierz **skryptów narzędzia Gradle** > **build.gradle (moduł: aplikacji)**.
2.  W obszarze **zależności**, wklej następujący kod:

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>O tym pakiecie

W powyższym kodzie pakiet biblioteki uwierzytelniania firmy Microsoft. MSAL obsługuje pobieranie, buforowanie i odświeżanie tokenów użytkownika, które umożliwiają dostęp do interfejsów API, które są chronione przez punkt końcowy usługi Azure Active Directory w wersji 2.
<!--end-collapse-->

## <a name="create-the-application-ui"></a>Tworzenie aplikacji interfejsu użytkownika

1. Przejdź do **res** > **układu**, a następnie otwórz **activity_main.xml**. 
2. Zmian układu działania z `android.support.constraint.ConstraintLayout` lub innych do `LinearLayout`.
3. Dodaj `android:orientation="vertical"` właściwości `LinearLayout` węzła.
4. Wklej następujący kod do `LinearLayout` węzła, zastępując bieżący zawartości:

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
