# spinnaker-deployment
spinnaker-deployment to deploy k8s objects

# 1)
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                                          Next Day:
#                                                       Git bash Window: 1
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

# Important Note: 
# This is the successful implementation log for the spinnaker deployment
# Next day deployment instructions:
# mysql ip will change every restart, so you need to change the IP Address in this file: /home/spinnaker/.hal/default/profiles/front50-local.yml &
# redeploy Spinnaker: ie, you need to rerun:   hal deploy apply from halyard container.
# change the new pod name, while doing the port-forward
# Kill running docker container & deploy halyard, only if redploy not working after running the above command, hal deploy apply


# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                            key commands performed in the first window:
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
kubectl get po -owide # note down the new IP address
kubectl get po -n spinnaker -owide
kubectl run mysql --image=mariadb:10.2 --env="MYSQL_ROOT_PASSWORD"="123" --env="MYSQL_DATABASE"="front50" # optional if it is already running
docker ps -q
docker stop $(docker ps -q)
docker rm $(docker ps -a -q)
docker kill $(docker ps -q)
docker ps
pwd
ls -al .hal # (if .hal not found create that directory -> mkdir ~/.hal)
docker run --name halyard \
    -v ~/.kube:/home/spinnaker/.kube \
    -v ~/.hal:/home/spinnaker/.hal \
    -it us-docker.pkg.dev/spinnaker-community/docker/halyard:stable
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl get po -owide
NAME    READY   STATUS    RESTARTS      AGE   IP           NODE             NOMINATED NODE   READINESS GATES
mysql   1/1     Running   9 (12h ago)   10d   10.1.0.152   docker-desktop   <none>           <none>

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl get po -n spinnaker -owide
NAME                               READY   STATUS             RESTARTS       AGE    IP           NODE             NOMINATED NODE   READINESS GATES
spin-clouddriver-8dc68d885-897ph   1/1     Running            2 (12h ago)    2d9h   10.1.0.150   docker-desktop   <none>           <none>
spin-deck-6fb59b4984-9xq5z         1/1     Running            8 (12h ago)    7d9h   10.1.0.139   docker-desktop   <none>           <none>
spin-echo-589769cbbd-drqvp         0/1     Running            5 (12h ago)    3d8h   10.1.0.142   docker-desktop   <none>           <none>
spin-echo-8585d77788-q4722         0/1     Running            2 (12h ago)    2d9h   10.1.0.153   docker-desktop   <none>           <none>
spin-front50-5cc8678d7f-v7rr4      0/1     CrashLoopBackOff   80 (44s ago)   2d9h   10.1.0.144   docker-desktop   <none>           <none>
spin-front50-cd6bb85b6-xcvhl       0/1     CrashLoopBackOff   83 (65s ago)   2d9h   10.1.0.148   docker-desktop   <none>           <none>
spin-gate-596569776f-m4ztt         1/1     Running            8 (12h ago)    7d9h   10.1.0.154   docker-desktop   <none>           <none>
spin-orca-6b4df7f7d6-l6hfv         1/1     Running            9 (12h ago)    7d8h   10.1.0.145   docker-desktop   <none>           <none>
spin-redis-d8b99f6d8-v6lr5         1/1     Running            8 (12h ago)    7d9h   10.1.0.149   docker-desktop   <none>           <none>
spin-rosco-5c65868766-5pq8n        1/1     Running            8 (12h ago)    7d8h   10.1.0.140   docker-desktop   <none>           <none>

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS        PORTS     NAMES
176617b8f2ed   nginx          "/docker-entrypoint.…"   13 hours ago   Up 13 hours             k8s_nginx-t-m1_nginx-t-m1-cf5b6bf85-wmk46_temenos-new_19d25a5e-09fd-46ae-93d5-5f1d7e96df23_7
3bcd551da25c   0fee7d19f5e6   "/opt/gate/bin/gate"     13 hours ago   Up 13 hours             k8s_gate_spin-gate-596569776f-m4ztt_spinnaker_8d546df8-cdbd-4140-95c9-e6a440ed18f9_8
001d20dac3f1   d0e651f6c750   "/opt/echo/bin/echo"     13 hours ago   Up 13 hours             k8s_echo_spin-echo-8585d77788-q4722_spinnaker_40ba272c-f2de-411d-b7eb-d744fd355b5d_2
d83792e07134   625e5b493bbb   "docker-entrypoint.s…"   13 hours ago   Up 13 hours             k8s_mysql_mysql_default_594519c9-3a66-4cda-9ada-25a0af22f38c_9
0a391890dc0e   ec20c3753c4c   "/opt/clouddriver/bi…"   13 hours ago   Up 13 hours             k8s_clouddriver_spin-clouddriver-8dc68d885-897ph_spinnaker_2a6cacc5-2b92-4099-8a77-8737354d1b06_2
3cf56749dbae   895b6824ecff   "docker-entrypoint.s…"   13 hours ago   Up 13 hours             k8s_redis_spin-redis-d8b99f6d8-v6lr5_spinnaker_9d354e2d-8242-4ae6-898c-238662787b86_8
a10e818f4bb5   0f96c0f05915   "/opt/orca/bin/orca"     13 hours ago   Up 13 hours             k8s_orca_spin-orca-6b4df7f7d6-l6hfv_spinnaker_0e92e388-6e0f-4542-9954-ec65531b73a6_9
bc8015dfebf6   d0e651f6c750   "/opt/echo/bin/echo"     13 hours ago   Up 13 hours             k8s_echo_spin-echo-589769cbbd-drqvp_spinnaker_e54a4cab-f136-49a6-aad9-69f9680b7abd_5
9013bcb3bcef   a765070631cb   "/opt/rosco/bin/rosco"   13 hours ago   Up 13 hours             k8s_rosco_spin-rosco-5c65868766-5pq8n_spinnaker_72923535-c7ec-41c6-902f-5852bb5315cc_8
a0b49ad57862   510617c1f213   "/bin/sh -c docker/r…"   13 hours ago   Up 13 hours             k8s_deck_spin-deck-6fb59b4984-9xq5z_spinnaker_da688406-347d-4326-b7e2-fcbb62b9e63e_8

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker ps -q
176617b8f2ed
3bcd551da25c
001d20dac3f1
d83792e07134
0a391890dc0e
3cf56749dbae
a10e818f4bb5
bc8015dfebf6
9013bcb3bcef
a0b49ad57862

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker stop $(docker ps -q)
176617b8f2ed
3bcd551da25c
001d20dac3f1
d83792e07134
0a391890dc0e
3cf56749dbae
a10e818f4bb5
bc8015dfebf6
9013bcb3bcef
a0b49ad57862

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker rm $(docker ps -a -q)
a4867ef43cf9
Error response from daemon: cannot remove container "/k8s_deck_spin-deck-6fb59b4984-9xq5z_spinnaker_da688406-347d-4326-b7e2-fcbb62b9e63e_9": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_mysql_mysql_default_594519c9-3a66-4cda-9ada-25a0af22f38c_10": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_nginx-t-m1_nginx-t-m1-cf5b6bf85-wmk46_temenos-new_19d25a5e-09fd-46ae-93d5-5f1d7e96df23_8": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_echo_spin-echo-8585d77788-q4722_spinnaker_40ba272c-f2de-411d-b7eb-d744fd355b5d_3": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_gate_spin-gate-596569776f-m4ztt_spinnaker_8d546df8-cdbd-4140-95c9-e6a440ed18f9_9": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_clouddriver_spin-clouddriver-8dc68d885-897ph_spinnaker_2a6cacc5-2b92-4099-8a77-8737354d1b06_3": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_echo_spin-echo-589769cbbd-drqvp_spinnaker_e54a4cab-f136-49a6-aad9-69f9680b7abd_6": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_orca_spin-orca-6b4df7f7d6-l6hfv_spinnaker_0e92e388-6e0f-4542-9954-ec65531b73a6_10": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_rosco_spin-rosco-5c65868766-5pq8n_spinnaker_72923535-c7ec-41c6-902f-5852bb5315cc_9": container is running: stop the container before removing or force remove
Error response from daemon: cannot remove container "/k8s_redis_spin-redis-d8b99f6d8-v6lr5_spinnaker_9d354e2d-8242-4ae6-898c-238662787b86_9": container is running: stop the container before removing or force remove

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker kill $(docker ps -q)
1abb2ce6e0ab
480969c51398
0f688b5b850d
8ad9952faa49
d2ada80d3d7a
2528d419aebb
1bb9f75077cb
126aaa8d966d
46bbf7d2923d
199df4a7c114

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ pwd
/c/Users/unniv

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ cd .hal

unniv@LAPTOP-P9K625G6 MINGW64 ~/.hal (master)
$ ls -al
total 20
drwxr-xr-x 1 unniv 197609 0 Jul 12 20:56 ./
drwxr-xr-x 1 unniv 197609 0 Jul 19 19:15 ../

unniv@LAPTOP-P9K625G6 MINGW64 ~/.hal (master)
$ docker run --name halyard \
    -v ~/.kube:/home/spinnaker/.kube \
    -v ~/.hal:/home/spinnaker/.hal \
    -it us-docker.pkg.dev/spinnaker-community/docker/halyard:stable


WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.codehaus.groovy.reflection.CachedClass (file:/opt/halyard/lib/groovy-2.5.14.jar) to method java.lang.Object.finalize()
WARNING: Please consider reporting this to the maintainers of org.codehaus.groovy.reflection.CachedClass
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
 _           _                     _
| |__   __ _| |_   _  __ _ _ __ __| |
| '_ \ / _` | | | | |/ _` | '__/ _` |
| | | | (_| | | |_| | (_| | | | (_| |
|_| |_|\__,_|_|\__, |\__,_|_|  \__,_|
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++ 




# 2)
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                                       Git bash Window: 2
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                            key commands performed in the second window:
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
i)
docker exec -it halyard bash
echo $HOME
hal config version
hal config version edit --version 1.34.3 # get the latest version from the above command & use it.
hal config provider kubernetes enable
# hal config provider kubernetes account add another-account --context docker-desktop
hal config provider kubernetes account add uk-spinnaker --context docker-desktop
# hal config deploy edit --type distributed --account-name another-account
hal config deploy edit --type distributed --account-name uk-spinnaker
hal config storage edit --type redis
hal config features edit --artifacts true

ii)
 mkdir -p /home/spinnaker/.hal/default/profiles/
 vi /home/spinnaker/.hal/default/profiles/front50-local.yml
 ls -l /home/spinnaker/.hal/default/profiles/front50-local.yml
 chmod 777 /home/spinnaker/.hal/default/profiles/front50-local.yml
 ls -l /home/spinnaker/.hal/default/profiles/front50-local.yml


iii)
# /home/spinnaker/.hal/default/profiles/front50-local.yml
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#validate DB ip address is correct or not
sql:
  enabled: true   
  connectionPools:
    default:
      default: true
      #jdbcUrl: jdbc:mysql://MYSQL_IP_ADDRESS:3306/front50
      jdbcUrl: jdbc:mysql://10.1.0.152:3306/front50
      user: root
      password: 123
  migration:
    user: root
    password: 123
    #jdbcUrl: jdbc:mysql://MYSQL_IP_ADDRESS:3306/front50
    jdbcUrl: jdbc:mysql://10.1.0.152:3306/front50
spinnaker:
  redis:
    enabled: false
# ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


iv)
cat .kube/config

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURCVENDQWUyZ0F3SUJBZ0lJVG53c0xHMzFtMHN3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRBMk1UWXdNVFUyTURGYUZ3MHpOREEyTVRRd01qQXhNREZhTUJVeApFekFSQmdOVkJBTVRDbXQxWW1WeWJtVjBaWE13Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLCkFvSUJBUURDeklhT3FaK3J4azQ5M0Y1ME5hYjdIdmVUeFZiZlc1Ris1dmFGNEx1NlNSYUhXNFNCYmMvSnUxckkKeWtNVjU2YmRVR1VLck9PTzZiUGt1bnpVbVFEaVlZcGZrTytna25NRmVybTJSZ2dGNHllcEthL0RvTkdjYmxFSQpoM1dJZVNmbjZvNnpjVnpiV2pWQ2FpZ1prM2xjb3VySjE5WkVDMUkrWkR1OHNoWjQ5L2ZQOFYrNzhLNXZQdnRjClNzcGFSTUx0TC9DOFdyZ2dBV2w5emxJb0Q4SWJNaW9kV1N1SWpHQnVmNURMQXd6SlBjU0ZGSndLNm1hUm5xRmcKTUs5Ylcyb1lpdFozakZNNkwyZU9lOHJvbk11bElNM2xEZCtvbHRCMGZDNDFpemhDd1R4V2d4bC9TZVV3eDViUgpwbExNN2dMSEowem5WV2R5TTZIbGppZEE5bE9MQWdNQkFBR2pXVEJYTUE0R0ExVWREd0VCL3dRRUF3SUNwREFQCkJnTlZIUk1CQWY4RUJUQURBUUgvTUIwR0ExVWREZ1FXQkJTQ1pSeldHK3RiekpGaTN2dlMvZi9uVFczNld6QVYKQmdOVkhSRUVEakFNZ2dwcmRXSmxjbTVsZEdWek1BMEdDU3FHU0liM0RRRUJDd1VBQTRJQkFRQk9Kb0Fuc2pyTQpycldMZnNhSjRjY3l3dHJxQjlYbGZKQmYyZ2lLN3huQjZBd0hEc3hJRjBZZ29MbjJ2ckVnaEYyWUNMdXNvV0dmCmo4N0FCZHVYZ2FJLzRWaGNoeFZmbDdvcXhOMjNWeENTNFRNWUx6dnVJUmN2OFJ0RTRUWXBrTGJldWY3VzhvRm8KK01DTEpXZkpVRXI2TTljQXRxWWdGazJiVDZDRE4zWFg5SHJGZ2VRK3k4THZVdE1ickNaL05RNTVRQmxwYTJPYQpqZTVTMnJVYzVPa1BlMjA1KzFZblV5ZUhIdWMwUUErc0NBaDFtUk1IdGdyTnVQYVRWL2k2TWZzSnEzYklCY2pVClkzMWhRV05YSUNhc0tEK2xYdWxiTEV3Y3Z5U2lPcm1GSUhhN3d4UE45RncyN2V2Nk04VG03aVZ2OWovOEwwK0gKTEo4STQvaXc1WW84Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://kubernetes.docker.internal:6443
  name: docker-desktop
contexts:
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: docker-desktop
kind: Config
preferences: {}
users:
- name: docker-desktop
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURRakNDQWlxZ0F3SUJBZ0lJQnJHTWRFWnRyNGd3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TkRBMk1UWXdNVFUyTURGYUZ3MHlOVEEzTVRNd056UXpOREphTURZeApGekFWQmdOVkJBb1REbk41YzNSbGJUcHRZWE4wWlhKek1Sc3dHUVlEVlFRREV4SmtiMk5yWlhJdFptOXlMV1JsCmMydDBiM0F3Z2dFaU1BMEdDU3FHU0liM0RRRUJBUVVBQTRJQkR3QXdnZ0VLQW9JQkFRRGtCSDBaQmFLU0VLWGYKYmZ5YnRVaG5aWGhhcGpUL0Q5Y1VTTDdTckhPOFR4TEtVV1oralJtTmlPZmpNMVVQdXVCbE1JNGFUV1pEWDJJSwpSY3E3MlQzczJJejJDeGcyZDRxUlRBYnBQbmluMWZ0SWJIQTltYUhEQjRBaXpYQ2ErMktEZ0tkdWQxTldrczJzCmVycUo1bXVTc1RxSU5vdHpQMnp4cjdzdzM0R1duZk94aXh5K29WQk1XVE9kakt5WkV6d3h5MlhpSFljUHNKb3oKT2dOaDlsajhaU21ZMTlwQ0NNTXV4Q216SGU4VjFHcUFvTjI2RSt0eEs3TFdSbUZOMlRMa04yRUpncndDbEJmawpiRjMxYUNnWlpCYXdJR2NlaHduMzJtM0Y3aXBleHJYTE9UaUNmVUh4YjhYa3dKSDBPK3Q1SzdkbW9OQ2dhcUFkClJsNXFqRlRMQWdNQkFBR2pkVEJ6TUE0R0ExVWREd0VCL3dRRUF3SUZvREFUQmdOVkhTVUVEREFLQmdnckJnRUYKQlFjREFqQU1CZ05WSFJNQkFmOEVBakFBTUI4R0ExVWRJd1FZTUJhQUZJSmxITlliNjF2TWtXTGUrOUw5LytkTgpiZnBiTUIwR0ExVWRFUVFXTUJTQ0VtUnZZMnRsY2kxbWIzSXRaR1Z6YTNSdmNEQU5CZ2txaGtpRzl3MEJBUXNGCkFBT0NBUUVBWTNzYitiWHl5VFNIMW9hcjBEM21OYml1eUJMSWVOamJiUEl1WWlieklCSDMzclVGMGhGWTJjalgKSUhBUFRGR09PYnhCdVVQRi9sTUVTVXpUVkRrRGNXb1dycjgyTWFkRERPNzRyRUs0Nm5wU3I3ano4cEo4TWtwNAo3dFVINkkycVJsbk4rb3FEK3lUbFNyaHUwMElMR1J5N1FON293S0lwS2JDcHZBNmlXQWNBeWQ2R3ZRTmZJbFJNCnQzb25GVkU2SW1ObEJuMTdpOW1YV05YbGg2Y2sxaDgwN1RKVmIzZVoweTUwMGNzN0NHN2czaFIzK1IwRU9SOG0KNi9vQUtqMU56K2cxd3RhNDBHQWtFM2FXMkNHSStCTC9MZWU4L1g4SkxPRm5NMVdvTm9mKy95Q01NaVRaS2l4cwozRzU3ZG9uMEhPUTRmQXVqMENOMWllSHJaNEdDZ0E9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
    client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFcEFJQkFBS0NBUUVBNUFSOUdRV2lraENsMzIzOG03VklaMlY0V3FZMC93L1hGRWkrMHF4enZFOFN5bEZtCmZvMFpqWWpuNHpOVkQ3cmdaVENPR2sxbVExOWlDa1hLdTlrOTdOaU05Z3NZTm5lS2tVd0c2VDU0cDlYN1NHeHcKUFptaHd3ZUFJczF3bXZ0aWc0Q25ibmRUVnBMTnJIcTZpZVpya3JFNmlEYUxjejlzOGErN01OK0JscDN6c1lzYwp2cUZRVEZrem5ZeXNtUk04TWN0bDRoMkhEN0NhTXpvRFlmWlkvR1VwbU5mYVFnakRMc1Fwc3gzdkZkUnFnS0RkCnVoUHJjU3V5MWtaaFRka3k1RGRoQ1lLOEFwUVg1R3hkOVdnb0dXUVdzQ0JuSG9jSjk5cHR4ZTRxWHNhMXl6azQKZ24xQjhXL0Y1TUNSOUR2cmVTdTNacURRb0dxZ0hVWmVhb3hVeXdJREFRQUJBb0lCQUducEw5WTBpTnZCd2RvegpNMWQ3NytLZS9qVlU2RjdsRTZrSFZTTjBHTm9KL0pkZkVmV01RcWM4UlVHQ01XdDZDUXJYYjlvRnc0OWR1YU05CjRibzVZUXNuVnVwZUl1UUJ5S3BPbnhNdUFrc3JHR24rZkhBUWNWT3VvelVIS2R2REVuSTBpeEREZWtGSE5TTjkKYWpIdGhsLy8xRWs2MUxFMmI3c2ZQaHFvWm4vM2c2WGFkekVKektuQitLVWxwWlhYYVh2ZTlSUVhxdEw2alNMNwpsQWF2emFZaTRCRzI3M0xKNWN0KzBLTzNWTDgvcnBoVEZsTC9qNm9RUFpVUXpZMXRjRFpoNkttckVZVVAyWGxyCnNXUXhFSW90bGZ6M0V4RjZFeUVLeXVacHdBajBaYnZzbGtRdWNIVGZ6VE5zQVR2dnM0MUJnbEtoZWhuUnI0ck0KbHFHNndVa0NnWUVBN2VGYWtWUXlUNnRZWjM1TURFdFZEb1JJcXBCRHIyTWp0dCszUFpWbVBsN1V5NCtveXFROApKQkRrNkNDRFNNOU55Tmp2OTZ1R1pCV3oxck05aVNWNW8xRFU2TmJ0c0E3cGVJeXZaUDdjYWFWVGUzRUJxUHR4Ck1iQi9vNXVpeDQ4RXRiVnNRZ1RXWFgyN0gvWU9PTVZKenlDSFg1VHR3Zndhek5kb0xYOGNrYzBDZ1lFQTlXTFAKM2I0YUZXNExHSXBGbjdHdkZJd1VEM00yNDJJaE1UaUxGTlRRYzhhWEswYy9XV2ZRZ0g1ZVR4VWw5Zng3c3oxYwpma3dQKzZaaCtQQUNkTWxnbE5taFE3TUdwdUlBeEZvQjVFU0t4M01oWFpjZ2p4YW5rbFdOQjZGTGVZZFdrYU5CCjl5NnY3VzVRRjdiREQ3Vk82R1EwYmg3YUVFM3lKWCsrRWg3aVNQY0NnWUVBMFE2SUJjaTFIb2M3Zmxqa2hMbC8KMnU0dTNwZENwTlRBQW12Q0lvRTJCZ0paaVR5TDN1dFBYM0xwZVlkNVdYZTJxSmplN0E3MllPY2gwY0pjaGpkRgpHMTVpZnU3RW5VRzl0WkFPWlBnR3hXSGlhRTM4WTRIM0RQQk1mSUMvSWhweXpTN1FJWVVXejdzN3hPNzl1SzdLCkd4by9YcVVIL3IrTTNoRkJJUnJoQk5FQ2dZQjl1Rjk1WTFGbjAxWjUvelhHcUxsajFKVGVpWkFBbGFFZDM3TmEKTytLRXZxVnF0ZmtkN0ZObDBkaXR1YzhXSllLWEJZUWk3UU52VWdKMkVUbWc3aCsrZzNYZEFwY1EzMCszU2NvWQpVb21yd0MvRERJL1EzN0dCQnJYWHgrcXNYQXVkV0VDT3pPdXp3TVhtTUd2bDIyV1BLTyszejdTM2o4ZlZNSXFJCkxDeVJad0tCZ1FEYjlOOW02VTFNRUdYekliKytIWkMxVzE0M0N2Ujk4ZElabGo5ZzNRWE9mc25LZ3NFRkNSUDEKbi9mcStZSksyQTZtb21sRlp6MGpUTDdQWCtiQUdVSlAxS3dZd3ZSL3RvM0dWYnJyeWF2aUw4cTlZN1VwT0Z5WQpGZFE2dXYwcTB1M2kzQm5vT2xnOXlsamdwbldDZ3ZyaEpabitwSmF1cFdwRG11eHRXVVVxNWc9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=


v)
mkdir -p /home/spinnaker/.kube
vi /home/spinnaker/.kube/config
ls -al /home/spinnaker/.kube/config


vi)
hal deploy apply
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ docker exec -it halyard bash
bash-5.0$ pwd
/
bash-5.0$ ls -al
total 64
drwxr-xr-x    1 root     root          4096 Jul 19 20:13 .
drwxr-xr-x    1 root     root          4096 Jul 19 20:13 ..
-rwxr-xr-x    1 root     root             0 Jul 19 20:13 .dockerenv
drwxrwxrwx    1 root     root          4096 Jul 12 09:02 \Program Files\Git\home\spinnaker\.hal
drwxrwxrwx    1 root     root          4096 Jul  9 09:29 \Program Files\Git\home\spinnaker\.kube
drwxr-xr-x    1 root     root          4096 Sep 14  2022 bin
drwxr-xr-x    5 root     root           360 Jul 19 20:13 dev
drwxr-xr-x    1 root     root          4096 Jul 19 20:13 etc
drwxr-xr-x    1 root     root          4096 Sep 14  2022 home
drwxr-xr-x    1 root     root          4096 Nov 12  2021 lib
drwxr-xr-x    5 root     root          4096 Nov 12  2021 media
drwxr-xr-x    2 root     root          4096 Nov 12  2021 mnt
drwxr-xr-x    1 root     root          4096 Sep 14  2022 opt
dr-xr-xr-x  361 root     root             0 Jul 19 20:13 proc
drwx------    1 root     root          4096 Sep 14  2022 root
drwxr-xr-x    2 root     root          4096 Nov 12  2021 run
drwxr-xr-x    2 root     root          4096 Nov 12  2021 sbin
drwxr-xr-x    2 root     root          4096 Nov 12  2021 srv
dr-xr-xr-x   11 root     root             0 Jul 19 20:13 sys
drwxrwxrwt    1 root     root          4096 Jul 19 20:13 tmp
drwxr-xr-x    1 root     root          4096 Sep 14  2022 usr
drwxr-xr-x    1 root     root          4096 Nov 12  2021 var
bash-5.0$ echo $HOME
/home/spinnaker
bash-5.0$ ls -al \Program Files\Git\home\spinnaker\.hal
ls: Program: No such file or directory
ls: FilesGithomespinnaker.hal: No such file or directory
bash-5.0$
bash-5.0$ hal config version edit --version 1.34.3
+ Get current deployment
  Success
+ Edit Spinnaker version
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Spinnaker has been configured to update/install version "1.34.3".
  Deploy this version of Spinnaker with `hal deploy apply`.
bash-5.0$
bash-5.0$
bash-5.0$
bash-5.0$ hal config provider kubernetes enable
+ Get current deployment
  Success
+ Edit the kubernetes provider
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

Validation in default.provider.kubernetes:
- WARNING Provider kubernetes is enabled, but no accounts have been
  configured.

+ Successfully enabled kubernetes
bash-5.0$
bash-5.0$
bash-5.0$
bash-5.0$ hal config provider kubernetes account add uk-spinnaker --context docker-desktop
+ Get current deployment
  Success
+ Add the uk-spinnaker account
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully added account uk-spinnaker for provider
  kubernetes.
bash-5.0$
bash-5.0$
bash-5.0$
bash-5.0$ hal config deploy edit --type distributed --account-name uk-spinnaker
+ Get current deployment
  Success
+ Get the deployment environment
  Success
+ Edit the deployment environment
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully updated your deployment environment.
bash-5.0$
bash-5.0$
bash-5.0$
bash-5.0$ hal config storage edit --type redis
+ Get current deployment
  Success
+ Get persistent storage settings
  Success
+ Edit persistent storage settings
  Success
Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully edited persistent storage.
bash-5.0$
bash-5.0$
bash-5.0$ hal config features edit --artifacts true
+ Get current deployment
  Success
+ Get features
  Success
+ Edit features
  Success
Validation in default.features:
- WARNING Field Features.artifacts not supported for Spinnaker
  version 1.34.3: Artifacts are now enabled by default.
? You no longer need this.

Validation in halconfig:
- WARNING There is a newer version of Halyard available (1.65.0),
  please update when possible
? Run 'sudo apt-get update && sudo apt-get install
  spinnaker-halyard -y' to upgrade

+ Successfully updated features.
bash-5.0$
bash-5.0$
bash-5.0$
bash-5.0$ mkdir -p /home/spinnaker/.hal/default/profiles/
bash-5.0$
bash-5.0$ vi /home/spinnaker/.hal/default/profiles/front50-local.yml
bash-5.0$
bash-5.0$
bash-5.0$ mkdir -p /home/spinnaker/.kube
  Success
+ Deploy spin-front50
  Success
+ Deploy spin-orca
  Success
+ Deploy spin-deck
  Success
+ Deploy spin-echo
  Success
+ Deploy spin-gate
  Success
+ Deploy spin-rosco
  Success
+ Run `hal deploy connect` to connect to Spinnaker.
bash-5.0$
bash-5.0$
bash-5.0$ whoami
spinnaker
bash-5.0$
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++




# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                                       3) Git bash Window: 3
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
#                                            key commands performed in the third window:
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
whoami
kubectl -n spinnaker get po -owide
kubectl get po -owide
kubectl -n spinnaker port-forward spin-deck-<> 9000 &
kubectl -n spinnaker port-forward spin-gate-<> 8084

==> To get Spinnaker GUI, From browser:
    localhost:9000  
#++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++


unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ whoami
unniv

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl -n spinnaker get po -owide
NAME                                READY   STATUS    RESTARTS       AGE    IP           NODE             NOMINATED NODE   READINESS GATES
spin-clouddriver-56bc5bf5d7-lf5rc   1/1     Running   0              74s    10.1.0.157   docker-desktop   <none>           <none>
spin-deck-6fb59b4984-9xq5z          1/1     Running   10 (23m ago)   7d9h   10.1.0.139   docker-desktop   <none>           <none>
spin-echo-8585d77788-q4722          0/1     Running   4 (23m ago)    2d9h   10.1.0.153   docker-desktop   <none>           <none>
spin-echo-8bb755c48-rhppj           0/1     Running   0              74s    10.1.0.155   docker-desktop   <none>           <none>
spin-front50-65c75675cc-hpdl5       1/1     Running   0              74s    10.1.0.156   docker-desktop   <none>           <none>
spin-gate-596569776f-m4ztt          1/1     Running   10 (23m ago)   7d9h   10.1.0.154   docker-desktop   <none>           <none>
spin-orca-6b4df7f7d6-l6hfv          1/1     Running   11 (23m ago)   7d9h   10.1.0.145   docker-desktop   <none>           <none>
spin-redis-d8b99f6d8-v6lr5          1/1     Running   10 (23m ago)   7d9h   10.1.0.149   docker-desktop   <none>           <none>
spin-rosco-5c65868766-5pq8n         1/1     Running   10 (23m ago)   7d9h   10.1.0.140   docker-desktop   <none>           <none>

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl get po -owide
NAME    READY   STATUS    RESTARTS       AGE   IP           NODE             NOMINATED NODE   READINESS GATES
mysql   1/1     Running   11 (23m ago)   10d   10.1.0.152   docker-desktop   <none>           <none>

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl -n spinnaker port-forward spin-deck-6fb59b4984-9xq5z 9000 &
[1] 688

unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ Forwarding from 127.0.0.1:9000 -> 9000
Forwarding from [::1]:9000 -> 9000


unniv@LAPTOP-P9K625G6 MINGW64 ~ (master)
$ kubectl -n spinnaker port-forward spin-gate-596569776f-m4ztt 8084
Forwarding from 127.0.0.1:8084 -> 8084
Forwarding from [::1]:8084 -> 8084
Handling connection for 9000
Handling connection for 9000
Handling connection for 9000
Handling connection for 8084
Handling connection for 8084
Handling connection for 8084
Handling connection for 8084
# +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

