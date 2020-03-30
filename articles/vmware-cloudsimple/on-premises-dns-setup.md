---
title: Rozwiązanie Azure VMware firmy CloudSimple — konfigurowanie usługi DNS dla chmury prywatnej CloudProste
description: W tym artykule opisano sposób konfigurowania rozpoznawania nazw DNS w celu uzyskania dostępu do serwera vCenter w chmurze CloudSimple Private Cloud z lokalnych stacji roboczych
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c2d69d21eb46d502a45c9df1dfaaa947d26ef7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246112"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Konfigurowanie systemu DNS dla rozpoznawania nazw dla dostępu vCenter usługi Private Cloud z lokalnych stacji roboczych

Aby uzyskać dostęp do serwera vCenter w chmurze cloudimobiej CloudSimple z lokalnych stacji roboczych, należy skonfigurować rozpoznawanie adresów DNS, aby serwer vCenter mógł być adresowany za pomocą nazwy hosta, a także adresu IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Uzyskiwanie adresu IP serwera DNS dla chmury prywatnej

1. Zaloguj się do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Przejdź do chmury**prywatnej** **zasobów** > i wybierz chmurę prywatną, z którą chcesz się połączyć.

3. Na stronie **Podsumowanie** chmury prywatnej w obszarze **Informacje podstawowe**skopiuj adres IP serwera DNS w chmurze prywatnej.

    ![Serwery DNS w chmurze prywatnej](media/private-cloud-dns-server.png)


Użyj jednej z tych opcji dla konfiguracji DNS.

* [Tworzenie strefy na serwerze DNS dla *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Utwórz warunkową usługę przesyłania dalej na lokalnym serwerze DNS w celu rozwiązania *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Tworzenie strefy na serwerze DNS dla *.cloudsimple.io

Strefę można skonfigurować jako strefę skrótową i wskazać serwery DNS w chmurze prywatnej w celu rozpoznawania nazw. Ta sekcja zawiera informacje dotyczące korzystania z serwera DNS BIND lub serwera DNS systemu Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Tworzenie strefy na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od indywidualnej konfiguracji DNS.

Na przykład dla domyślnej konfiguracji serwera BIND, edytuj /etc/named.conf pliku na serwerze DNS i dodać następujące informacje o strefie.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Tworzenie strefy na serwerze DNS systemu Microsoft Windows

1. Kliknij prawym przyciskiem myszy serwer DNS i wybierz polecenie **Nowa strefa**. 
  
    ![Nowa strefa](media/DNS01.png)
2. Wybierz **opcję Strefa skrótowa** i kliknij przycisk **Dalej**.

    ![Nowa strefa](media/DNS02.png)
3. Wybierz odpowiednią opcję w zależności od środowiska i kliknij przycisk **Dalej**.

    ![Nowa strefa](media/DNS03.png)
4. Wybierz **pozycję Strefa wyszukiwania do przodu** i kliknij przycisk **Dalej**.

    ![Nowa strefa](media/DNS01.png)
5. Wprowadź nazwę strefy i kliknij przycisk **Dalej**.

    ![Nowa strefa](media/DNS05.png)
6. Wprowadź adresy IP serwerów DNS dla chmury prywatnej uzyskane z portalu CloudSimple.

    ![Nowa strefa](media/DNS06.png)
7. Kliknij **przycisk Dalej zgodnie** z potrzebami, aby ukończyć konfigurację kreatora.

## <a name="create-a-conditional-forwarder"></a>Tworzenie warunkowego przesyłania dalej

Usługa przesyłania dalej warunkowej przesyła dalej wszystkie żądania rozpoznawania nazw DNS do wyznaczonego serwera. W przypadku tej konfiguracji każde żądanie do *.cloudsimple.io jest przekazywane do serwerów DNS znajdujących się w chmurze prywatnej. Poniższe przykłady pokazują, jak skonfigurować usługi przesyłania dalej na różnych typach serwerów DNS.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS BIND

Określony plik i parametry do skonfigurowania mogą się różnić w zależności od indywidualnej konfiguracji DNS.

Na przykład dla domyślnej konfiguracji serwera BIND, edytuj /etc/named.conf pliku na serwerze DNS i dodać następujące informacje o przekazywaniu warunkowym.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Tworzenie usługi przesyłania dalej warunkowej na serwerze DNS systemu Microsoft Windows

1. Otwórz Menedżera DNS na serwerze DNS.
2. Kliknij prawym przyciskiem myszy **warunkowe przesyłanie dalej** i wybierz opcję, aby dodać nową warunkową przesyłanie dalej.

    ![Usługa warunkowa przesyłania dalej 1 Usługa Windows DNS](media/DNS08.png)
3. Wprowadź domenę DNS i adres IP serwerów DNS w chmurze prywatnej, a następnie kliknij przycisk **OK**.
