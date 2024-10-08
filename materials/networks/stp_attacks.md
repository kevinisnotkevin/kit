# STP attacks

Атака помогает разорвать связующие деревья (spanning-trees) у свитчей, дестабилизировать их таблицы с MAC-адресами и удерживать сеть в непрерывном состоянии повторного выбора корневого моста. 

Мы можем сделать это, поскольку не существует механизма аутентификации, встроенного в протокол STP. Путем создания BPDU-фреймов несуществующего свитча с ID=1, мы можем выбрать несуществующий свитч в качестве корневого моста. Используя минимальное значение max-age в наших пакетах и не посылая пакеты в течение этого времени, мы спровоцируем повторный выбор корневого моста, а затем в то время, когда будут проходить перевыборы, вновь будем отсылать пакеты, выигрывать эти выборы и становиться корневым мостом.

Итак, Spanning Tree Protocol предназначен для предотвращения петель в сетевой инфраструктуре.

Чтобы не было петель, часть интерфейсов коммутаторов заблокируются, чтобы до каждого из устройств был только один путь от корневого коммутатора.

Злоумышленник как раз хочет стать главной всей топологии. Для проведения атаки он подключится к двум коммутаторам и пошлем им BPDU фрейм с данными, сформированными таким образом, чтобы все остальные подумали, что компьютер злоумышленника является главой топологии. В данных злоумышленник попросту укажет минимально возможный id. Этому компьютеру передадут роль root, а лишние связи снова заблокируются, чтобы не создавалось новых петель.

По итогу для связи между некоторыми коммутаторами в топологии трафик будет проходить как раз через компьютер злоумышленника, ведь все связи коммутаторов осуществляются через главный коммутатор. А захватив роль roota, компьютер злоумышленника стал тем самым главным коммутатором. Он, в свою очередь, сможет просмотреть трафик, который ему по умолчанию не предназначался. Такая атака не даст просмотреть весь трафик в сети. Если трафик направляется от левого верхнего коммутатора в левый средний, то до главного коммутатора такая информация не пройдет и злоумышленник ее не перехватит.