# Развертывание Remote Desktop Gateway


{% include [ms-disclaimer](../../_includes/ms-disclaimer.md) %}



Remote Desktop Gateway (RDGW) — сервис Windows Server для доступа к ресурсам, у которых нет выхода в интернет, через защищенный канал связи по протоколу HTTPS. 

В сценарии пользователи из группы `Administrators` получат доступ к тестовой виртуальной машине с использованием самоподписанного сертификата.

Чтобы развернуть инфраструктуру Remote Desktop Gateway:

1. [Подготовьте облако к работе](#before-you-begin).
1. [Создайте облачную сеть и подсети](#create-network).
1. [Создайте группу безопасности](#sg).
1. [Создайте ВМ для шлюза](#create-gw).
1. [Настройте роль RDGW](#role).
1. [Проверьте работу шлюза RDGW](#test-rdgw).

Если созданные ресурсы вам больше не нужны, [удалите их](#clear-out).

## Подготовьте облако к работе {#before-you-begin}

{% include [before-you-begin](../_tutorials_includes/before-you-begin.md) %}

{% include [ms-additional-data-note](../_tutorials_includes/ms-additional-data-note.md) %}


### Необходимые платные ресурсы {#paid-resources}

В стоимость инсталляции RDGW входят:

* плата за постоянно запущенные виртуальные машины (см. [тарифы {{ compute-full-name }}](../../compute/pricing.md));
* плата за использование динамических или статических публичных IP-адресов (см. [тарифы {{ vpc-full-name }}](../../vpc/pricing.md));
* стоимость исходящего трафика из {{ yandex-cloud }} в интернет (см. [тарифы {{ compute-full-name }}](../../compute/pricing.md)).


## Создайте облачную сеть и подсети {#create-network}

Создайте облачную сеть `rdgw-network` с подсетью в зоне доступности, где будут находиться виртуальные машины.

1. Создайте [облачную сеть](../../vpc/concepts/network.md).

   {% list tabs group=instructions %}

   - Консоль управления {#console}
    
        1. Выберите сервис **{{ vpc-short-name }}** в каталоге, где требуется создать облачную сеть.
        1. Нажмите кнопку **Создать сеть**.
        1. Задайте имя сети `rdgw-network`.
        1. Нажмите кнопку **Создать сеть**.
   
    - CLI {#cli}
      
      ```
      yc vpc network create --name rdgw-network
      ```

      Где `rdgw-network` — имя сети.

      Результат:
      
      ```
      id: qqppl6fduhct76qkjh6s
      folder_id: big67u7m5flplkc6vvpc
      created_at: "2021-06-09T10:49:07Z"
      name: rdgw-network
      default_security_group_id: enpa139ji55jti00u5sg
      ```

    - API {#api}

      Воспользуйтесь методом REST API [create](../../vpc/api-ref/Network/create.md) для ресурса [Network](../../vpc/api-ref/Network/index.md) или вызовом gRPC API [NetworkService/Create](../../vpc/api-ref/grpc/Network/create.md).

   {% endlist %}

1. Создайте подсеть в сети `rdgw-network`.

   {% list tabs group=instructions %}
   
    - Консоль управления {#console}
    
        1. Выберите сервис **{{ vpc-short-name }}** в каталоге, где требуется создать подсеть.
        1. Нажмите на имя облачной сети.
        1. Нажмите кнопку **Добавить подсеть**.
        1. Заполните форму: введите имя подсети `rdgw-subnet`, выберите нужную зону доступности из выпадающего списка (например, `{{ region-id }}-d`).
        1. Введите CIDR подсети: IP-адрес и маску подсети: `10.1.0.0/16`. Подробнее про диапазоны IP-адресов в подсетях читайте в разделе [Облачные сети и подсети](../../vpc/concepts/network.md).
        1. Нажмите кнопку **Создать подсеть**.
   
    - CLI {#cli}
   
      ```
      yc vpc subnet create `
        --name rdgw-subnet `
        --zone {{ region-id }}-d `
        --network-name rdgw-network `
        --range 10.1.0.0/16
      ```

      Где `rdgw-subnet` — имя подсети.
      
      Результат:
      
      ``` 
      id: e9b95m6al33r********
      folder_id: big67u7m5flp********
      created_at: "2021-06-09T10:49:21Z"
      name: rdgw-subnet
      network_id: qqppl6fduhct********
      zone_id: {{ region-id }}-d
      v4_cidr_blocks:
      - 10.1.0.0/16
      ```

    - API {#api}

       Воспользуйтесь методом REST API [create](../../vpc/api-ref/Subnet/create.md) для ресурса [Subnet](../../vpc/api-ref/Subnet/index.md) или вызовом gRPC API [SubnetService/Create](../../vpc/api-ref/grpc/Subnet/create.md).

   {% endlist %}

## Создайте группу безопасности {#sg}

Создайте и настройте [группу безопасности](../../vpc/concepts/security-groups.md).

{% list tabs group=instructions %}

- Консоль управления {#console}

   1. Выберите сервис **{{ vpc-short-name }}** в каталоге, где требуется создать группу безопасности.
   1. Откройте вкладку **Группы безопасности**.
   1. Нажмите кнопку **Создать группу**.
   1. Введите имя группы безопасности `my-rdgw-sg`.
   1. В поле **Сеть** выберите сеть, которой будет назначена группа безопасности — `rdgw-network`.
   1. В блоке **Правила** создайте следующие правила для управления трафиком по инструкции под таблицей:
      
        | Направление</br>трафика | Описание | Диапазон</br>портов | Протокол | Тип</br>источника | Источник/Назначение | 
        |---|---|---|---|---|---|
        | Входящий | icmp | — | ICMP | CIDR | 0.0.0.0/0 |
        | Входящий | self-security | Любой | Любой | Группа безопасности | Текущая |
        | Входящий | tcp | 3389 | TCP | CIDR | 0.0.0.0/0 |
        | Входящий | rdgw | 443 | TCP | CIDR | 0.0.0.0/0 |
        | Исходящий | default | Любой | Любой | CIDR | 0.0.0.0/0 |
        
        1. Выберите вкладку **Исходящий трафик** или **Входящий трафик**.
        1. Нажмите кнопку **Добавить правило**.
        1. В открывшемся окне в поле **Диапазон портов** укажите один порт или диапазон портов, куда или откуда будет поступать трафик.
        1. В поле **Протокол** укажите нужный протокол или оставьте **Любой**, чтобы разрешить передачу трафика по всем протоколам.
        1. В поле **Назначение** или **Источник** выберите назначение правила:
           * **CIDR** — правило будет применено к диапазону IP-адресов. В поле **CIDR блоки** укажите CIDR и маски подсетей, в которые или из которых будет поступать трафик. Чтобы добавить несколько CIDR, нажимайте кнопку **Добавить CIDR**.
           * **Группа безопасности** — правило будет применено к ВМ из текущей группы или из выбранной группы безопасности.
        1. Нажмите кнопку **Сохранить**. Таким образом создайте все правила из таблицы.
     
   1. Нажмите кнопку **Сохранить**.
   
- CLI {#cli}

   ```
   yc vpc security-group create --name=my-rdgw-sg `
     --network-name rdgw-network `
     --rule direction=ingress,protocol=icmp,v4-cidrs=[0.0.0.0/0],description=icmp `
     --rule direction=ingress,port=any,protocol=any,predefined=self_security_group,description=self `
     --rule direction=ingress,port=3389,protocol=tcp,v4-cidrs=[0.0.0.0/0],description=rdp `
     --rule direction=ingress,port=443,protocol=tcp,v4-cidrs=[0.0.0.0/0],description=rdgw `
     --rule direction=egress,port=any,protocol=any,v4-cidrs=[0.0.0.0/0],description=default 
   ```
  
   Результат:

   ``` 
   id: enp136p8s2ae********
   folder_id: big67u7m5flp********
   created_at: "2021-06-09T10:50:29Z"
   name: my-rdgw-sg
   network_id: qqppl6fduhct********
   status: ACTIVE
   rules:
   - id: env98jerk9b3********
     description: icmp
     direction: INGRESS
     protocol_name: ICMP
     protocol_number: "1"
     cidr_blocks:
     v4_cidr_blocks:
      - 0.0.0.0/0
  ```

- API {#api}

  Воспользуйтесь методом REST API [create](../../vpc/api-ref/SecurityGroup/create.md) для ресурса [SecurityGroup](../../vpc/api-ref/SecurityGroup/index.md) или вызовом gRPC API [SecurityGroupService/Create](../../vpc/api-ref/grpc/SecurityGroup/create.md).

{% endlist %}

## Создайте ВМ для шлюза {#create-gw}

Создайте ВМ с публичным адресом:

{% list tabs group=instructions %}

- Консоль управления {#console}

     1. На странице каталога в [консоли управления]({{ link-console-main }}) нажмите кнопку **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}** и выберите `{{ ui-key.yacloud.iam.folder.dashboard.value_compute }}`.
     1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_image }}**:

         * Перейдите на вкладку **{{ ui-key.yacloud.compute.instances.create.image_value_custom_new }}**.
         * Нажмите кнопку **{{ ui-key.yacloud.common.select }}** и в открывшемся окне выберите **{{ ui-key.yacloud.common.create }}**.
         * В поле **{{ ui-key.yacloud.compute.instances.create-disk.field_source }}** выберите `{{ ui-key.yacloud.compute.instances.create-disk.value_source-image }}` и в списке ниже выберите образ **Windows Server 2022 Datacenter**. Как загрузить свой образ для продуктов Microsoft подробнее см. в разделе [Импортировать нужный образ](../../microsoft/byol.md#how-to-import).
         * (Опционально) В поле **{{ ui-key.yacloud.compute.field_additional }}** включите опцию **{{ ui-key.yacloud.compute.field_disk-autodelete }}**, если вы хотите автоматически удалять этот диск при удалении ВМ.
         * Нажмите кнопку **{{ ui-key.yacloud.compute.component.instance-storage-dialog.button_add-disk }}**.
     1. В блоке **{{ ui-key.yacloud.k8s.node-groups.create.section_allocation-policy }}** выберите [зону доступности](../../overview/concepts/geo-scope.md) `{{ region-id }}-d`.
     1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_storages }}** задайте размер загрузочного [диска](../../compute/concepts/disk.md) `60 {{ ui-key.yacloud.common.units.label_gigabyte }}`.
     1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_platform }}** перейдите на вкладку `{{ ui-key.yacloud.component.compute.resources.label_tab-custom }}` и укажите необходимую [платформу](../../compute/concepts/vm-platforms.md), количество vCPU и объем RAM:

         * **{{ ui-key.yacloud.component.compute.resources.field_platform }}** — `Intel Ice Lake`.
         * **{{ ui-key.yacloud.component.compute.resources.field_cores }}** — `2`.
         * **{{ ui-key.yacloud.component.compute.resources.field_core-fraction }}** — `100%`.
         * **{{ ui-key.yacloud.component.compute.resources.field_memory }}** — `4 {{ ui-key.yacloud.common.units.label_gigabyte }}`.
     1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_network }}** укажите:

         * **{{ ui-key.yacloud.component.compute.network-select.field_subnetwork }}** — сеть `rdgw-network` и подсеть `rdgw-subnet`.
         * **{{ ui-key.yacloud.component.compute.network-select.field_external }}** — `{{ ui-key.yacloud.component.compute.network-select.switch_auto }}`.
         * **{{ ui-key.yacloud.component.compute.network-select.field_security-groups }}** — `my-rdgw-sg`.
     1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_base }}** задайте имя ВМ: `my-rds-gw`.
     1. Нажмите кнопку **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

     {% include [vm-reset-password-windows-operations](../../_includes/compute/reset-vm-password-windows-operations.md) %}

- CLI {#cli}
     
    1. В терминале PowerShell создайте скрипт `setpass` для настройки пароля учетной записи `Administrator` через поле `user-data` в [метаданных ВМ](../../compute/concepts/vm-metadata.md). Утилита `cloudbase-init` выполнит его при первом запуске.

       {% note info %}
    
       Первая строка скрипта обязательно должна содержать только `#ps1`, иначе утилита `cloudbase-init` не выполнит скрипт.
    
       {% endnote %}
    
        ```powershell
        #ps1
        Get-LocalUser | Where-Object SID -like *-500 | Set-LocalUser -Password (ConvertTo-SecureString "<ваш пароль>" -AsPlainText -Force) 
        ```
   
    1. Создайте ВМ и укажите в качестве источника метаданных созданный скрипт. В качестве параметра `security-group-ids` вставьте идентификатор группы безопасности `my-rdgw-group`:

       ```
       yc compute instance create `
         --name my-rds-gw `
         --hostname my-rds-gw `
         --memory 4 `
         --cores 2 `
         --platform standard-v3 `
         --zone {{ region-id }}-d `
         --network-interface subnet-name=rdgw-subnet,ipv4-address=10.1.0.3,nat-ip-version=ipv4,security-group-ids=<id_my-rdgw-group> `
         --create-boot-disk image-folder-id=standard-images,image-family=windows-2022-dc-gvlk `
         --metadata-from-file user-data=setpass
       ```

       Результат:

       ``` 
       done (25s)
       id: frmogfp7mm1k********
       folder_id: big67u7m5flp********
       created_at: "2021-06-09T10:51:58Z"
       name: my-rds-gw
       zone_id: {{ region-id }}-d
       platform_id: standard-v3
       resources:
       memory: "4294967296"
       cores: "2"
       core_fraction: "100"
       status: RUNNING
       boot_disk:
       mode: READ_WRITE
       device_name: fhmplfvr7g6p********
       auto_delete: true
       disk_id: fhmplfvr7g6p********
       network_interfaces:
       - index: "0"
         mac_address: d0:0d:18:83:c8:7b
         subnet_id: e9b95m6al33r********
         primary_v4_address:
         address: 10.1.0.3
         one_to_one_nat:
         address: 178.154.231.126
         ip_version: IPV4
         security_group_ids:
          - enp136p8s2a********
            fqdn: my-rds-gw.{{ region-id }}.internal
            scheduling_policy: {}
            network_settings:
            type: STANDARD
            placement_policy: {}
       ```

       {% include [cli-metadata-variables-substitution-notice](../../_includes/compute/create/cli-metadata-variables-substitution-notice.md) %}

{% endlist %}

## Настройте роль RDGW {#role}

1. [Подключитесь](../../compute/operations/vm-connect/rdp.md) к созданной ВМ по протоколу RDP.
1. Установите необходимые роли и [оснастки управления]({{ ms.docs }}/troubleshoot/windows-server/system-management-components/what-is-microsoft-management-console#more-information):
   
    ```powershell
    Install-WindowsFeature RDS-Gateway -IncludeManagementTools
    ```
   
   Результат:
   
   ``` 
   Success Restart Needed Exit Code      Feature Result
   ------- -------------- ---------      --------------
   True    No             Success        {Network Policy and Access Services, Remot...
   ```
   
1. Импортируйте модуль RDS:
   
   ```powershell
   Import-Module -Name RemoteDesktopServices
   ```
   
   
1. Создайте политику клиентского доступа, чтобы все учетные записи локальной группы `Administrators` могли подключаться к шлюзу RDGW. Сделать это можно через [диск Windows PowerShell]({{ ms.docs }}/powershell/scripting/samples/managing-windows-powershell-drives?view=powershell-7.1), который будет создан автоматически при импорте роли:

   ```powershell
   New-Item -Path 'RDS:\GatewayServer\CAP' -Name 'Default-CAP' -UserGroups Administrators@Builtin -AuthMethod '1'
   ```

   Результат:

   ``` 
       Directory: RDS:\GatewayServer\CAP
   
   Name                   Type      CurrentValue         GP   PermissibleValues PermissibleOperations
   ----                   ----      ------------         --   ----------------- ---------------------
   Default-CAP                                           -                      Get-Item, Get-ChildItem, Remove-Item,...
   ```

1. Создайте политику доступа к ресурсам, которая разрешит всем учетным записям локальной группы `Administrators` подключаться к любому внутреннему ресурсу через созданный шлюз RDGW. 

   ```powershell
   New-Item -Path 'RDS:\GatewayServer\RAP' -Name 'Default-RAP' -UserGroups Administrators@Builtin -ComputerGroupType '2'
   ```

   Результат:

   ``` 
       Directory: RDS:\GatewayServer\RAP
   
   Name                   Type      CurrentValue         GP   PermissibleValues PermissibleOperations
   ----                   ----      ------------         --   ----------------- ---------------------
   Default-RAP                                           -                      Get-Item, Get-ChildItem, Remove-Item,...
   ```
   
1. Создайте сертификат с именем сервера, который будет использоваться для подключения и шифрования трафика:

   ```powershell
   $Certificate = New-SelfSignedCertificate -CertStoreLocation 'Cert:\LocalMachine\My' -DnsName $ENV:COMPUTERNAME
   ```
   
1. Экспортируйте созданный сертификат, чтобы в дальнейшем загрузить его в хранилище сертификатов `Trusted Roots Certificate Authorities` на компьютере, подключающемся к ВМ. Это необходимо, чтобы клиент доверял самоподписанному сертификату шлюза RDG:

   ```powershell
   $Certificate | Export-Certificate -FilePath "C:\REGW.cer"
   ```
   
   Результат:

   ``` 
       Directory: RDS:\GatewayServer\RAP
   
   Name                   Type      CurrentValue         GP   PermissibleValues PermissibleOperations
   ----                   ----      ------------         --   ----------------- ---------------------
   Default-RAP                                           -                      Get-Item, Get-ChildItem, Remove-Item,...
   ```

1. Укажите, какой сертификат нужно использовать шлюзу RDGW. Перезагрузите службу RDG, чтобы применить настройки:

   ```powershell
   Set-Item -Path 'RDS:\GatewayServer\SSLCertificate\Thumbprint' -Value $Certificate.Thumbprint
   Get-Service TSGateway | Restart-Service
   ```

   Результат:

   ``` 
       Directory: C:\
   
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   -a----         6/9/2021  11:51 AM            796 REGW.cer
   ```

Виртуальная машина-шлюз с настроенной ролью RDGW позволяет локальным учетным записям группы `BUILTIN\Administrators` подключаться к ВМ, у которых напрямую нет доступа в интернет.

## Проверьте работу шлюза RDGW {#test-rdgw}

1. Создайте ВМ без доступа в интернет, к которой вы будете подключаться во время проверки. 

    {% list tabs group=instructions %}

    - Консоль управления {#console}

        1. На странице каталога в [консоли управления]({{ link-console-main }}) нажмите кнопку **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}** и выберите `{{ ui-key.yacloud.iam.folder.dashboard.value_compute }}`.
        1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_image }}**:

            * Перейдите на вкладку **{{ ui-key.yacloud.compute.instances.create.image_value_custom_new }}**.
            * Нажмите кнопку **{{ ui-key.yacloud.common.select }}** и в открывшемся окне выберите **{{ ui-key.yacloud.common.create }}**.
            * В поле **{{ ui-key.yacloud.compute.instances.create-disk.field_source }}** выберите `{{ ui-key.yacloud.compute.instances.create-disk.value_source-image }}` и в списке ниже выберите образ **Windows Server 2022 Datacenter**. Как загрузить свой образ для продуктов Microsoft подробнее см. в разделе [Импортировать нужный образ](../../microsoft/byol.md#how-to-import).
            * (Опционально) В поле **{{ ui-key.yacloud.compute.field_additional }}** включите опцию **{{ ui-key.yacloud.compute.field_disk-autodelete }}**, если вы хотите автоматически удалять этот диск при удалении ВМ.
            * Нажмите кнопку **{{ ui-key.yacloud.compute.component.instance-storage-dialog.button_add-disk }}**.
        1. В блоке **{{ ui-key.yacloud.k8s.node-groups.create.section_allocation-policy }}** выберите [зону доступности](../../overview/concepts/geo-scope.md) `{{ region-id }}-d`.
        1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_storages }}** задайте размер загрузочного диска `60 {{ ui-key.yacloud.common.units.label_gigabyte }}`.
        1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_platform }}** перейдите на вкладку `{{ ui-key.yacloud.component.compute.resources.label_tab-custom }}` и укажите необходимую [платформу](../../compute/concepts/vm-platforms.md), количество vCPU и объем RAM:

            * **{{ ui-key.yacloud.component.compute.resources.field_platform }}** — `Intel Ice Lake`.
            * **{{ ui-key.yacloud.component.compute.resources.field_cores }}** — `2`.
            * **{{ ui-key.yacloud.component.compute.resources.field_core-fraction }}** — `100%`.
            * **{{ ui-key.yacloud.component.compute.resources.field_memory }}** — `4 {{ ui-key.yacloud.common.units.label_gigabyte }}`.
        1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_network }}** укажите:

            * **{{ ui-key.yacloud.component.compute.network-select.field_subnetwork }}** — сеть `rdgw-network` и подсеть `rdgw-subnet`.
            * **{{ ui-key.yacloud.component.compute.network-select.field_external }}** — `{{ ui-key.yacloud.component.compute.network-select.switch_none }}`.
        1. В блоке **{{ ui-key.yacloud.compute.instances.create.section_base }}** задайте имя ВМ: `test-vm`.
        1. Нажмите кнопку **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

        {% include [vm-reset-password-windows-operations](../../_includes/compute/reset-vm-password-windows-operations.md) %}

    - CLI {#cli}
        
      Создайте ВМ:
    
      ```
      yc compute instance create `
        --name test-vm `
        --hostname test-vm `
        --memory 4 `
        --cores 2 `
        --platform standard-v3 `
        --zone {{ region-id }}-d `
        --network-interface subnet-name=rdgw-subnet,ipv4-address=10.1.0.4 `
        --create-boot-disk image-folder-id=standard-images,image-family=windows-2022-dc-gvlk `
        --metadata-from-file user-data=setpass
      ```

      Результат:
   
      ``` 
      done (19s)
      id: fhm5pflreh2j********
      folder_id: big67u7m5flp********
      created_at: "2021-06-09T11:53:03Z"
      name: test-vm
      zone_id: {{ region-id }}-d
      platform_id: standard-v3
      resources:
      memory: "4294967296"
      cores: "2"
      core_fraction: "100"
      status: RUNNING
      boot_disk:
      mode: READ_WRITE
      device_name: fhmmf65nlbt1********
      auto_delete: true
      disk_id: fhmmf65nlbt1********
      network_interfaces:
      - index: "0"
        mac_address: d0:0d:5d:ef:bb:74
        subnet_id: e9b95m6al33r********
        primary_v4_address:
        address: 10.1.0.4
        fqdn: test-vm.{{ region-id }}.internal
        scheduling_policy: {}
        network_settings:
        type: STANDARD
        placement_policy: {}
      ```

    - API {#api}

      Воспользуйтесь методом REST API [create](../../compute/api-ref/Instance/create.md) для ресурса [Instance](../../compute/api-ref/Instance/) или вызовом gRPC API [InstanceService/Create](../../compute/api-ref/grpc/Instance/create.md).

    {% endlist %} 

1. Импортируйте созданный сертификат в папку `Trusted Roots Certificate Authorities` на компьютере, с которого вы будете подключаться к тестовой ВМ.
    
   Чтобы подключаться к шлюзу по имени ВМ, в файле `C:\Windows\system32\drivers\etc\hosts` укажите имя и внешний IP-адрес созданного шлюза RDGW. Например:
   
    ```powershell
    87.250.250.242 my-rds-gw
    ```
   
1. Запустите [утилиту `mstsc`]({{ ms.docs }}/windows-server/administration/windows-commands/mstsc), которая создает подключения к удаленному рабочему столу. В настройках на вкладке **Advanced** укажите в качестве шлюза имя ВМ `my-rds-gw`, в качестве узла назначения — имя тестовой ВМ `test-vm`, имя пользователя — `Administrator`. 

## Как удалить созданные ресурсы {#clear-out}

Если вам больше не нужны созданные ресурсы, удалите [виртуальные машины](../../compute/operations/vm-control/vm-delete.md) и [сети](../../vpc/operations/network-delete.md).
