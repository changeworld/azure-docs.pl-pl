---
title: 'Szybki start: tworzenie aplikacji Python korzystającej z usługi Azure Cache for Redis | Microsoft Docs'
description: W tym przewodniku Szybki start dowiesz się, jak utworzyć aplikację Python korzystającą z usługi Azure Cache for Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/11/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 73c14b3d3023dcca113589d63276216fcfdd17f1
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513445"
---
# <a name="quickstart-use-azure-cache-for-redis-with-python"></a>Szybki start: korzystanie z usługi Azure Cache for Redis za pomocą języka Python


## <a name="introduction"></a>Wprowadzenie

W tym przewodniku Szybki start przedstawiono sposób nawiązywania połączenia z usługą Azure Cache for Redis przy użyciu języka Python w celu odczytywania i zapisywania danych w pamięci podręcznej. 

![Test dotyczący języka Python ukończony](./media/cache-python-get-started/cache-python-completed.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* [Środowisko Python 2 lub Python 3](https://www.python.org/downloads/) z zainstalowanym pakietem [pip](https://pypi.org/project/pip/). 

## <a name="create-an-azure-cache-for-redis-on-azure"></a>Tworzenie usługi Azure Cache for Redis na platformie Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Instalowanie klienta redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) to interfejs języka Python dla usługi Azure Cache for Redis. Użyj dostępnego w języku Python narzędzia do obsługi pakietów *pip*, aby zainstalować pakiet redis-py. 

W poniższym przykładzie użyto *pip3* dla środowiska python3 jako zainstalować pakiet redis-py w systemie Windows 10 przy użyciu programu Visual Studio 2019 wiersz polecenia dewelopera uruchomiona z podwyższonym poziomem uprawnień administratora.

```python
    pip3 install redis
```

![Instalowanie klienta redis-py](./media/cache-python-get-started/cache-python-install-redis-py.png)


## <a name="read-and-write-to-the-cache"></a>Odczytywanie i zapisywanie w pamięci podręcznej

Uruchom środowisko Python i przetestuj korzystanie z pamięci podręcznej z poziomu wiersza polecenia. Zastąp `<Your Host Name>` i `<Your Access Key>` wartościami odpowiednimi dla używanej pamięci podręcznej Azure Cache for Redis. 

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>.redis.cache.windows.net',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Dla usługi Redis jest wersja 3.0 lub wyższej, sprawdzenie certyfikatu SSL jest wymuszany. ssl_ca_certs musi być jawnie ustawione podczas nawiązywania połączenia z magazynem Redis. W przypadku systemu Linux RH ssl_ca_certs znajdują się w "/ etc/pki/tls/certs/ca-bundle.crt" moduł certyfikatu.

## <a name="create-a-python-script"></a>Tworzenie skryptu w języku Python

Utwórz nowy plik tekstowy skryptu o nazwie *PythonApplication1.py*.

Dodaj następujący skrypt do pliku *PythonApplication1.py* i zapisz go. Ten skrypt przetestuje dostęp do pamięci podręcznej. Zastąp `<Your Host Name>` i `<Your Access Key>` wartościami odpowiednimi dla używanej pamięci podręcznej Azure Cache for Redis. 

```python
import redis

myHostname = "<Your Host Name>.redis.cache.windows.net"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Uruchom skrypt języka Python.

![Test dotyczący języka Python ukończony](./media/cache-python-get-started/cache-python-completed.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz przejść do innego samouczka, możesz zachować zasoby utworzone w tym przewodniku Szybki start i użyć ich ponownie.

W przeciwnym razie po zakończeniu pracy z przykładową aplikacją poradnika Szybki start możesz usunąć zasoby platformy Azure utworzone w tym poradniku Szybki start, aby uniknąć naliczania opłat. 

> [!IMPORTANT]
> Usunięcie grupy zasobów jest nieodwracalne i grupa zasobów oraz wszystkie zawarte w niej zasoby zostaną trwale usunięte. Uważaj, aby nie usunąć przypadkowo niewłaściwych zasobów lub grupy zasobów. Jeśli zasoby do hostowania tego przykładu zostały utworzone wewnątrz istniejącej grupy zasobów zawierającej zasoby, które chcesz zachować, możesz usunąć każdy zasób oddzielnie z odpowiadającego mu bloku zamiast usuwać całą grupę zasobów.
>

Zaloguj się do witryny [Azure Portal](https://portal.azure.com) i kliknij pozycję **Grupy zasobów**.

W polu tekstowym **Filtruj według nazwy...** wpisz nazwę grupy zasobów. Instrukcje w tym artykule używają grupy zasobów o nazwie *TestResources*. Dla grupy zasobów na liście wyników kliknij pozycję **...** , a następnie kliknij pozycję **Usuń grupę zasobów**.

![Usuwanie](./media/cache-web-app-howto/cache-delete-resource-group.png)

Zobaczysz prośbę o potwierdzenie usunięcia grupy zasobów. Wpisz nazwę grupy zasobów w celu potwierdzenia, a następnie kliknij pozycję **Usuń**.

Po krótkim czasie grupa zasobów i wszystkie zawarte w niej zasoby zostaną usunięte.


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie prostej aplikacji internetowej platformy ASP.NET, która korzysta z usługi Azure Cache for Redis.](./cache-web-app-howto.md)



<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png
