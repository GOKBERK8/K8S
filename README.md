# K8S
## Kendim için k8s özellikleri ve kullanımı

## İçindekiler
- [Kubectl Config](#kubectl-config)
- [Kubectl Kullanımı](#kubectl-kullanımı)
- [POD](#pod)
- [İmperative Yöntem](#i̇mperative-yöntem)
- [YAML](#yaml)
- [Declarative Yöntem](#declarative-yöntem)
- [İnit Container](#i̇nit-container)
- [LABEL](#label)
- [Annotations](#annotations)
- [Namespace](#namespace)
- [Deployment](#deployment)
- [Rollout ve Rollback](#rollout-ve-rollback)
- [Service](#service)

## Kubectl Config 
- `kubectl config get-contexts` // mevcut contextleri listeler
- `kubectl config current-context` // kullanılan contexti söyler
- `kubectl config use-context "minikube"` // context değiştirmek için kullanılan komut burda ise minikube contextini varsayılan olarak ayarla diyoruz
- `kubectl get nodes` // varsayılan context i getirir, bilgilerini gösterir

## Kubectl Kullanımı
- `kubectl cluster-info` // kubernetes cluster ile ilgili temel bir bilgi edinmek için kullandığımız komut
- `kubectl` // bu komut ile kullanabileceğimiz komutları bize listeler
- `kubectl .... --help` // "...." ile belirttiğim yere herhangi bir komut yazıp yanına --help eklediğim zamanda bu kullandığım komutun nasıl kullanıldığı ve ne işe yaradığı hakkında bilgileri elde edebilirim
- `kubectl "fiil, yapmak istediğimiz işlem" "obje, tip, tür" "gerekirse obje tipinin ismi"`
- `kubectl get|delete|edit|apply pods testpods` // aşağı yukarı böyle gözükmesi gerekiyor
- `kubectl get pods` // sistem varsayılan namespace i ney ise ordaki podları listeler
- `kubectl get pods -n kube-system` // -n ile namespace i değiştirebiliriz bu örnekte kube-system altındaki podları listeledik
- `kubectl get pods -A` // namespace göz etmeksizin bütün podları listeler
- `kubectl get pods -A -o wide` // -o ile output yani biz komutu girdiğimizde bize çıktı olarak ne vermesini istediğimizi yazabiliyoruz bu örnekte genişletilmiş çıktı istedik
- `kubectl get pods -A -o yaml` // yaml formatında gösterir
- `kubectl get pods -A -o json` // json formatında gösterir
- `-o json|jsonpath|go-template|yaml|wide|custom-columns` // gibi formatları mevcut
- `kubectl explain pod` // explain komutu objeler hakkında bilgi verir bu komutta podlar hakkında bilgileri bize getirir

## POD

          - bir veya birden fazla container içeren kubernetes altındaki standart dağıtılabilir birimdir.

          - Her podun eşsiz bir id'si bulunur.
          
          - Kuberneteste oluşturabileceğimiz en temel obje poddur.

## İmperative Yöntem
          - Bu yöntemde satır satır kod girmemiz gerekmektedir.
### İlk POD'umuzu Oluşturalım

`kubectl run firstpod --image=nginx --restart=Never`
- run komutu ile oluşturuyoruz 
- bir isim atıyoruz (firstpod)
- hangi imajdan oluşacağını belirliyoruz 
- bu pod kapandığında yeniden başlatılıp başlatılmayacağını ayarlıyoruz

#### Pod'un daha detaylı bilgilerine erişelim
`kubectl describe pods firstpod` 
- describe subcommand ile detaylı bilgiye ulaşabiliriz
- Bu komut ile Events kısmına erişebiliyoruz bu kısım bir nevi oluşturduğumuz podun tarihçesidir.

#### Pod'un loglarına erişebiliriz
`kubectl logs firstpod` 
- Bu komut ile loglarına erişebiliriz.

`kubectl logs -f firstpod`
- Bu komut ile de anlık olarak logları inceleyebiliriz.

`kubectl exec firstpod -- hostname` 
- exec komutu ile oluşturduğumuz podun içinde komut çalıştırabiliriz.
- "--" den sonra çalıştırmak istediğimiz komutu girdiğimizde bize bu komutun çıktısını verecektir.

`kubectl exec -it firstpod -- /bin/sh` 
- Oluşturduğumuz podun shelline bağlanmak için kullandığımız komut

`kubectl delete pods firstpod` 
- Bu komut ile oluşturduğumuz podu silebilriz.

# YAML

      key: value
      anahtar: değer
      rakam: 1
      anahtar1: deger1

      fizik: <br/>
        boy: 1.78 <br/>
        kilo: 65 <br/>

      kimya:
        - su
        - hava

      kimya: [su, hava, degisti]

      kimya:
        duygu:
          - ismi: Sevgi
            puan: 100
          - ismi: Nefret
            puan: 20

      run: |
        npm install
        npm run build

            // anahtar ve değerler ile oluşturuyoruz
            // "|" ile alt alta kod çalıştırabiliyoruz
            // "-" ile array oluyor
            // boşluklar sayesinde objeler oluşturabiliyoruz
          
      ---> map 
      containers:
        - name: nginx
          image: nginx:1.6
          ports:
            - containerPorts: 80
 
      ---> kubernetes
      apiVersion: apps|v1 // kubernetes üzerinde kullanacağımız objenin versionu // kubectl explain pods ile öğrenebiliriz
      kind: pod|deployment|services // hangi objeleri kullanacağımızı belirler
      metadata: // isim, label, namespace gibi bilgileri tanımladığımız yer
      spec: // uygulamanın, objenin nasıl çalışacağı hangi bilgileri içereceği bilgilerini tutar

      ---> örnek yaml dosyası

      apiVersion: v1
      kind: Pod
      metadata:
        name: app
        namespace: default
      spec:
        containers:
        - name: app
          image: nginx

      ---> ikinci bir örnek yaml dosyası
      pod1.yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: firstpod
        labels:
          app: frontend
      spec:
        containers:
        - name: nginx
          image: nginx:latest
          ports:
          - containerPort: 80
# Declarative Yöntem

        Bu yöntem sayesinde tek tek yapmamıza gerek olmadan isteklerimizi birleştirerek verebiliriz. 
        Herhangi bir değişiklikte de tekrardan bu isteklerimiz içine yazarız ve değişiklik uygulanmış olur.

`kubectl apply -f pod1.yaml` 
- Bu yaml dosyası ile bir pod oluşturmak istediğimizde kullanmamız gereken kod
- Bu kod ile yaml dosyasında yapacağımız bir değişikliği silme, tekrar oluşturma işlemlerine gerek kalmadan configure edebiliriz.

`kubectl edit pods firstpod`
- Bu komut çok kullanılmasa da bu kod sayesinde yaml dosyamızın içeriğini değiştirebiliriz
- Bu kodun çok kullanılmamasının sebebi yapılan değişikliğin yaml dosyası üzerinde gözükmemesi 

## POD Yaşam Döngüsü
- Pending
- Creating
- ImagePullBackOff
- Running
- Succeeded
- Failed
- CrashLoopBackOff

`kubectl exec -it multicontainer -c sidecarcontainer -- /bin/sh`
- Bu kod ile multicontainer içindeki bir başka container a geçebiliriz.

### İnit Container

          apiVersion: v1
          kind: Pod
          metadata:
            name: initcontainerpod
          spec:
            containers:
            - name: appcontainer
              image: busybox
              command: ['sh', '-c', 'echo The app is running! && sleep 3600']
          initContainers:
          - name: initcontainer
            image: busybox
            command: ['sh', '-c', "until nslookup myservice.default.svc.cluster.local; do echo waiting for nslookup myservice.default.svc.cluster.local; sleep 2; done"]

- İnit containerlar bizim ana containerımız başlatılmadan önce yapılması gerekenleri yapan bir container
- İnit container tamamlanmadan ana container başlatılmaz

### LABEL
`kubectl get pods -l "app" --show-labels`
- Bu komut sayesinde podları listelerken app anahtarına sahip bütün podları listeler

`kubectl get pods -l "app=firstapp" --show-labels`
- Aralığı daha da daraltmak için değerini de verirsek aralığımız küçülür.

`kubectl get pods -l "app=firstapp,tier=frontend" --show-labels`
- "," koyarak ekstra olarak aralığımızı daraltabiliriz. "," ve anlamına gelmektedir.

`kubectl get pods -l "app=firstapp,tier!=frontend" --show-labels`
- "!" ile yazdığımız kriterin dışında olanları listeler.

**Farklı Bir Kullanım Olarak**

`kubectl get pods -l 'app in (firstapp)' --show-labels` 

- Bu komut sayesinde podları listelerken app anahtarına sahip bütün podları listeler

`kubectl get pods -l 'app in (firstapp,secondapp)' --show-labels`

- Burda ise app i firstapp ve secondapp değerlerine sahip olanları listeler

`kubectl get pods -l 'app notin (firstapp)' --show-labels`

- Bu komutta yazdığımızın dışındakileri listelemesini istiyoruz

`kubectl get pods -l 'app,app notin (firstapp)' --show-labels`

- Burda ise app anahtarı olanların içinde firstapp olmayanları listeliyoruz.

`kubectl get pods -l 'app,app notin (firstapp), tier in (frontend)' --show-labels`

`kubectl label pods pod9 app=thirdapp`

- Burda app anahtarının altında thirdapp değerinde yeni bir label eklemesini yapıyoruz.
- Bu labeli daha sonradan kaldırmak için ise

`kubectl label pods pod9 app-`

`kubectl label --overwrite pods pod9 team=team3`
- Bu kodla ise güncelleme yapabiliyoruz.

`kubectl label pods --all foo=bar`
- Bütün podlara label ekleme komutu.

### Annotations
          annotations:
              owner: "Gokberk Gokalan"
              notification-email: "admin@k8sfundamentals.com"
              releasedate: "01.01.2021"
              nginx.ingress.kubernetes.io/force-ssl-redirect: "true"

- Label olarak eklememizin sakıncalı olacağı bilgileri açıklama "annotation" olarak ekleriz

`kubectl apply -f podannotation.yaml`

`kubectl describe pods annotationpod`

Annotations:      nginx.ingress.kubernetes.io/force-ssl-redirect: true
                  notification-email: admin@k8sfundamentals.com
                  owner: Gokberk Gokalan
                  releasedate: 01.01.2021
  
- Bir nevi açıklamadır.

`kubectl annotate pods annotationpod foo=bar`

- Bu komut ile annotation ekleme yapabiliriz.

`kubectl annotate pods annotationpod foo-`

- Bu kodla da eklediğimiz annotation u kaldırabilriz.

### Namespace
`kubectl get pods --namespace kube-system`

- Sadece istediğimiz namesoace i listeler

`kubectl get pods -A`

- Bütün namespaceleri listeler.
  
          apiVersion: v1
          kind: Namespace
          metadata:
            name: development
          ---
          apiVersion: v1
          kind: Pod
          metadata:
            namespace: development
            name: namespacepod
          spec:
            containers:
            - name: namespacecontainer
              image: nginx:latest
              ports:
              - containerPort: 80
  
- Namespacelari yaml olarakta tanımlayabilriz.

`kubectl get pods -n development`

- Tanımlı oldupu namespace ile çağırılabilir.
 
- Sadece belli bir namespace üzerinde çalışacaksak Aşağıdaki kod ile varsayılan namespace i değiştirebiliriz.

`kubectl config set-context --current --namespace=development`

- Namespace silmek için ise;
  
`kubectl delete namespaces development`

### Deployment
- Deployment'lar sürekli güncelleme yapacağım aynı türden containerların oluşmasını sağlayan bir komut.

`kubectl create deployment firstdeployment --image=nginx:latest --replicas=2`

- Bu komut ile isim, imaj ve kaç tane olacağını girerek deployment oluşturabiliriz.

`kubectl delete pods firstdeployment-5b665bb68f-nbsqz`

- Bu komut ile sildiğimizde ise 1 tanesini sildiğimiz ve geriye 1 tane kaldığı için isteklerimizle uyuşmadığından yeniden 1 tane daha pod oluşturur

`kubectl set image deployment/firstdeployment nginx=httpd`

- İlk ayarladığımız imajı bu şekilde değiştirebiliriz.

`kubectl scale deployment firstdeployment --replicas=5`

- Bu komut ile kaç tane oluşturacağımızı değiştirebiliriz (arttırıp, azaltılabilir)

` kubectl delete deployments firstdeployment`

- Bu komut ile de oluşturduğumuz deployment ı kaldırabiliriz.

**Bu şekilde imperative şekilde oluşturup düzenlemeyi gördük.**

**Sırada ise Declarative şeklinde oluşturmaya bakalım**

          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: firstdeployment
            labels:
              team: development
          spec:
            replicas: 3
            selector:
              matchLabels:
                app: frontend
            template:
              metadata:
                name: examplepod
                labels:
                  app: frontend
              spec:
                containers:
                - name: nginx
                  image: nginx:latest
                  ports:
                  - containerPort: 80

`kubectl rollout undo deployment firstdeployment`

- Bu kod yaptığımız değişiklikleri geri almamızı sağlar.
- Replicaset sayesinde bu işlemi yapabiliriz.
- Replicaset objesi oluşturmak yerine deployment kullanırız bunlar beraber çalışmaktadır.

### Rollout ve Rollback

`kubectl edit deployment rolldeployment --record`

- Değiklik yapmamızı sağlar ve bu değişikliği record komutu ile kaydederiz.

`kubectl set image deployment rolldeployment nginx=httpd:alpine --record=true`

- Burda set komutu ile güncelleme yapıyoruz set ediyoruz.

`kubectl rollout history deployment rolldeployment`

- Bu komutta yaptığımız değişikliklerin tarihçesini revisionlarını bize listeler.

`kubectl rollout undo deployment rolldeployment --to-revision=3`

- Bu komut ile istediğimiz revision u tekrardan geri alabilme şansımız olmakta.

- Rollout yaparken sistemde bir sıkıntı çıktığı zaman ve hatayı bulmak istediğimiz zaman rollout'u durdurmamız için gereken kod

`kubectl rollout pause deployment rolldeployment`

- Devam ettirmek için ise gereken kod

`kubectl rollout resume deployment rolldeployment`

### Service

          apiVersion: v1
          kind: Service
          metadata:
            name: backend
          spec:
            type: ClusterIP
            selector:
              app: backend
            ports:
              - protocol: TCP
                port: 5000
                targetPort: 5000

`kubectl apply -f serviceClusterIP.yaml`

- Bu kod ile backend service kurduk.
- service/backend created
  
`kubectl get service`

           NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
           backend      ClusterIP   10.96.168.39   <none>        5000/TCP   12s

`kubectl get pods`

          NAME                       READY   STATUS    RESTARTS   AGE
          backend-7586b7b49-98svd    1/1     Running   0          3m30s
          backend-7586b7b49-j5w5j    1/1     Running   0          3m30s
          backend-7586b7b49-svntl    1/1     Running   0          3m30s
          frontend-fb99cc5df-fgzpp   1/1     Running   0          3m30s
          frontend-fb99cc5df-mbzsv   1/1     Running   0          3m30s
          frontend-fb99cc5df-rhpfs   1/1     Running   0          3m30s

`kubectl exec -it frontend-fb99cc5df-fgzpp -- bash`

- Bu komut ile bağlanıyoruz

`bash-5.0# nslookup backend`

          Server:         10.96.0.10
          Address:        10.96.0.10:53

          Name:   backend.default.svc.cluster.local
          Address: 10.96.168.39
  
- Burda bazı durumlarda doğrudan isim ile bağlanamıyoruz yani bu örnekte "backend" ismi ile bağlanabiliyoruz ama bazende "backend.default.svc.cluster.local" bu isimle bağlanmamız gerekiyor.

`kubectl apply -f serviceNodePort.yaml`

- service/frontend created

`kubectl get service`

          NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
          backend      ClusterIP   10.96.168.39   <none>        5000/TCP       3m35s
          frontend     NodePort    10.96.79.62    <none>        80:30924/TCP   21s

`minikube service --url frontend`

- http://127.0.0.1:64412
- Bu komut sayesinde bize bağlanabileceğimiz bir link oluşturuyor.

`curl http://127.0.0.1:64412`

- Bu komut ve bu adres ile dışardan bir kullanıcıymış gibi bağlanmayı deneyimlemeyi sağlıyor.

`kubectl expose deployment backend --type=ClusterIP --name=backend`

`kubectl expose deployment frontend --type=NodePort --name=frontend`

- Bu şekilde de oluşturabilriz.
          
`kubectl get endpoints`

          NAME         ENDPOINTS                                         AGE
          backend      10.244.0.5:5000,10.244.0.6:5000,10.244.0.7:5000   3m44s
          frontend     10.244.0.3:80,10.244.0.4:80,10.244.0.8:80         2m30s
          
`kubectl describe endpoints frontend`

          Addresses:          10.244.0.3,10.244.0.4,10.244.0.8
            NotReadyAddresses:  <none>
            Ports:
              Name     Port  Protocol
              ----     ----  --------
              <unset>  80    TCP

### LivenessProbe

          apiVersion: v1
          kind: Pod
          metadata:
            labels:
              test: liveness
            name: liveness-http
          spec:
            containers:
            - name: liveness
              image: k8s.gcr.io/liveness
              args:
              - /server
              livenessProbe:
                httpGet:
                  path: /healthz
                  port: 8080
                  httpHeaders:
                  - name: Custom-Header
                    value: Awesome
                initialDelaySeconds: 3
                periodSeconds: 3
          ---
          apiVersion: v1
          kind: Pod
          metadata:
            labels:
              test: liveness
            name: liveness-exec
          spec:
            containers:
            - name: liveness
              image: k8s.gcr.io/busybox
              args:
              - /bin/sh
              - -c
              - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
              livenessProbe:
                exec:
                  command:
                  - cat
                  - /tmp/healthy
                initialDelaySeconds: 5
                periodSeconds: 5
          ---
          apiVersion: v1
          kind: Pod
          metadata:
            name: goproxy
            labels:
              app: goproxy
          spec:
            containers:
            - name: goproxy
              image: k8s.gcr.io/goproxy:0.1
              ports:
              - containerPort: 8080
              livenessProbe:
                tcpSocket:
                  port: 8080
                initialDelaySeconds: 15
                periodSeconds: 20

          
- Hataları bazen kubernetes farketmeyebilir çünkü çalışır durumda gözükmesi kubernetes için yeterlidir.
- Bizim kodumuz çalışıyor gözüküp fakat bir şekilde bağlanamıyorsak gibi hataları bu sayede izlememizi sağlar.

### Readiness Probe
**Podun servis sunmaya hazır olduğunu check eder**
- Bu da Liveness Probe gibi oluşturulabilir.
- Deployment'ı güncellediğimiz zaman mesela "v2 imajı"
- Bu yeni imajla yeni bir pod oluşturuldu
- Yeni pod çalışmaya başladı
- Readiness check mekanizması çalışmaya başladı
- İnitialDelaySeconds (işlenmesi gereken istek süresi) kadar bekledi ardından ilk kontrol yapıldı
- Kontrol sonucu olumlu olduğunda pod service'e eklendi
- Eski pod'un service ile bağlantısı kesildi
- Eski pod henüz kapatılmadı. İşlenmesi gereken istekler var ise işlemeye devam etmesi için.
- Eski pod'un düzgünce kapatılması için sistem sigterm sinyali yolladı
- Pod üzerindeki işlemleri tamamladıktan sonra kendini kapattı.
          
### Resource Limits

          apiVersion: v1
          kind: Pod
          metadata:
            labels:
              test: requestlimit
            name: requestlimit
          spec:
            containers:
            - name: requestlimit
              image: ozgurozturknet/stress
              resources:
                requests:
                  memory: "64M"
                  cpu: "250m"
                limits:
                  memory: "256M"
                  cpu: "0.5"

- request kısmında minimum gereken kaynağı ayarlarız
- limits kısmında ise bu container'ın en fazla ne kadar kaynak kullanabileceğini ayarlarız.
- limit kısmında sistem daha fazla ram isteyebilir fakat biz kısıtladığımız için OOMKilled durumuna geçerek pod restart ediliyor.

### Environment Variable

          apiVersion: v1
          kind: Pod
          metadata:
            name: envpod
            labels:
              app: frontend
          spec:
            containers:
            - name: envpod
              image: ozgurozturknet/env:latest
              ports:
              - containerPort: 80
              env:
                - name: USER
                  value: "Ozgur"
                - name: database
                  value: "testdb.example.com"

### Volume

          volumeMounts:
              - name: directory-vol
                mountPath: /dir1
              - name: dircreate-vol
                mountPath: /cache
              - name: file-vol
                mountPath: /cache/config.json       
            volumes:
            - name: directory-vol
              hostPath:
                path: /tmp
                type: Directory
            - name: dircreate-vol
              hostPath:
                path: /cache
                type: DirectoryOrCreate
            - name: file-vol
              hostPath:
                path: /cache/config.json
                type: FileOrCreate

### Secret

          apiVersion: v1
          kind: Secret
          metadata:
            name: mysecret
          type: Opaque
          stringData:
            db_server: db.example.com
            db_username: admin
            db_password: P@ssw0rd!
            
`kubectl get secret`

          NAME       TYPE     DATA   AGE
          mysecret   Opaque   3      7s

`kubectl describe secret mysecret`

          Name:         mysecret
          Namespace:    default
          Labels:       <none>
          Annotations:  <none>
          
          Type:  Opaque
          
          Data
          ====
          db_password:  9 bytes
          db_server:    14 bytes
          db_username:  5 bytes

`kubectl create secret generic mysecret2 --from-literal=db_server=db.example.com --from-literal=db_username=admin --from-literal=db_password=password`

- Bu da farklı bir şekilde tanımlama
- Fakat bunu shell üzerinden yazdığım için birileri geçmişimden bunlara erişebilir

`kubectl create secret generic mysecret3 --from-file=db_server=server.txt --from-file=db_username=username.txt --from-file=db_password=password.txt`

- Shell geçmişinden verilerimin bulunmasını engellemek için txt dosyalarına yazarak koruyabilirim.

`kubectl create secret generic mysecret4 --from-file=config.json`

- Bunu bir json halinde de okutabiliriz.

**Yaml Türünde Secret Oluşturma**

          apiVersion: v1
          kind: Pod
          metadata:
            name: secretpodvolume
          spec:
            containers:
            - name: secretcontainer
              image: ozgurozturknet/k8s:blue
              volumeMounts:
              - name: secret-vol
                mountPath: /secret
            volumes:
            - name: secret-vol
              secret:
                secretName: mysecret3

`kubectl exec -it secretpodvolume -- bash`

          bash-5.0# cd secret
          bash-5.0# ls
          db_password  db_server    db_username
          bash-5.0# cat db_server
          db.example.combash-5.0#

**Diğer Bir Türü**

          apiVersion: v1
          kind: Pod
          metadata:
            name: secretpodenv
          spec:
            containers:
            - name: secretcontainer
              image: ozgurozturknet/k8s:blue
              env:
                - name: username
                  valueFrom:
                    secretKeyRef:
                      name: mysecret3
                      key: db_username
                - name: password
                  valueFrom:
                    secretKeyRef:
                      name: mysecret3
                      key: db_password
                - name: server
                  valueFrom:
                    secretKeyRef:
                      name: mysecret3
                      key: db_server          

`kubectl exec secretpodenv -- printenv`

          username=admin
          password=P@ssw0rd!
          server=db.example.com

**Bir Diğer Türü**

          apiVersion: v1
          kind: Pod
          metadata:
            name: secretpodenvall
          spec:
            containers:
            - name: secretcontainer
              image: ozgurozturknet/k8s:blue
              envFrom:
              - secretRef:
                  name: mysecret3

`kubectl exec secretpodenvall -- printenv`

          db_server=db.example.com
          db_username=admin
          db_password=P@ssw0rd!

**ConfigMap'lerde aynı şekilde secret'lar gibi oluşturuluyor**

**Fakat bu ikisinin bir farkı bulunmakta gizli tutmamız gereken veriler için secret kullanırız**

**ConfigMap'lerde gizli tutmamıza gerek olmayan bilgileri içerir**

`kubectl create configmap myconfigmap --from-literal=background=blue --from-file=a.txt`

### Node Affinity

          apiVersion: v1
          kind: Pod
          metadata:
            name: nodeaffinitypod1
          spec:
            containers:
            - name: nodeaffinity1
              image: ozgurozturknet/k8s
            affinity:
              nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                  - matchExpressions:
                    - key: app
                      operator: In #In, NotIn, Exists, DoesNotExist
                      values:
                      - blue

- Bu kodda "requiredDuringSchedulingIgnoredDuringExecution" kısmında anahtar app'in altında "blue" değeri olmadan oluşturulamaz çünkü required'dir

          apiVersion: v1
          kind: Pod
          metadata:
            name: nodeaffinitypod2
          spec:
            containers:
            - name: nodeaffinity2
              image: ozgurozturknet/k8s
            affinity:
              nodeAffinity:
                preferredDuringSchedulingIgnoredDuringExecution:
                - weight: 1
                  preference:
                    matchExpressions:
                    - key: app
                      operator: In
                      values:
                      - blue
                - weight: 2
                  preference:
                    matchExpressions:
                    - key: app
                      operator: In
                      values:
                      - red

- Burda ise "preffered" olduğu için app'in altında blue veya red yok ise başka bir node bulup oluşturur.
- Burda ekstra olarak weight kısmında weight i büyük olana öncelik verilir yani
- Varsa ve boş ise öncelik red yoksa blue o da yok ise kendi başka bir node bulup oluşturur.

          apiVersion: v1
          kind: Pod
          metadata:
            name: nodeaffinitypod3
          spec:
            containers:
            - name: nodeaffinity3
              image: ozgurozturknet/k8s
            affinity:
              nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                  - matchExpressions:
                    - key: app
                      operator: Exists #In, NotIn, Exists, DoesNotExist

- Burda ise "app" anahtarı required fakat değeri herhangi bir şey olabilir.
 
### Taint ve Toleration  

`kubectl taint node minikube platform=production:NoSchedule`

- Bu komut ile bir tolerasyon ekleyebiliyoruz
- Bu tolerasyonu kaldırmak için ise;

`kubectl taint node minikube platform-`

- Komutunu kullanırız.

**Test İçin Bir Pod Oluşturalım**

`kubectl run test --image=nginx --restart=Never`

- Bu podun bilgilerine baktığımızda bir uyarı ile karşılaşmaktayız.

`kubectl describe pod test`

Warning  FailedScheduling  29s   default-scheduler  0/1 nodes are available: 1 node(s) had untolerated taint {platform: production}. preemption: 0/1 nodes are available: 1 Preemption is not helpful for scheduling.

          toleratedpod1      0/1     Pending   0             0s
          toleratedpod2      0/1     Pending   0             0s

- Tolerasyon tanımladığımızdan, tolerasyon şartını sağlayana kadar pending'te kalırlar

          apiVersion: v1
          kind: Pod
          metadata:
            name: toleratedpod1
            labels:
              env: test
          spec:
            containers:
            - name: toleratedcontainer1
              image: ozgurozturknet/k8s
            tolerations:
            - key: "platform"
              operator: "Equal"
              value: "production"
              effect: "NoSchedule"
          ---
          apiVersion: v1
          kind: Pod
          metadata:
            name: toleratedpod2
            labels:
              env: test
          spec:
            containers:
            - name: toleratedcontainer2
              image: ozgurozturknet/k8s
            tolerations:
            - key: "platform"
              operator: "Exists"
              effect: "NoSchedule"  

- Toleration'u uyguladığımız vakit.

`kubectl apply -f podtoleration.yaml`

          toleratedpod1      0/1     ContainerCreating   0             0s
          toleratedpod2      0/1     ContainerCreating   0             0s
          toleratedpod1      1/1     Running             0             3s
          toleratedpod2      1/1     Running             0             5s

- Running aşamasına geçtiler.

`kubectl taint node minikube color=blue:NoExecute`

- Bu kodda var olmayan bir taint ekliyoruz ve NoExecute komutu ile bu taint'e sahip olmayanları kaldır komutu veriyoruz.

### Authentication

**Key ve CSR oluşturma**

`openssl genrsa -out gokberkgokalan.key 2048`

`openssl req -new -key gokberkgokalan.key -out gokberkgokalan.csr -subj "/CN=gokberk@gokberkgokalan.net/O=DevTeam"`

**CertificateSigningRequest oluşturma**

          apiVersion: certificates.k8s.io/v1
          kind: CertificateSigningRequest
          metadata:
            name: gokberkgokalan
          spec:
            groups:
            - system:authenticated
            request: $(cat gokberkgokalan.csr | base64 | tr -d "\n")
            signerName: kubernetes.io/kube-apiserver-client
            usages:
            - client auth
          EOF

**CSR onaylama ve crt'yi alma**

`kubectl get csr`

- Pending aşamasında gözükmekte

`kubectl certificate approve gokberkgokalan`

- Approved, Issued aşamasına geçti

`kubectl get csr gokberkgokalan -o jsonpath='{.status.certificate}' | base64 -d >> gokberkgokalan.crt`

**kubectl config ayarları**

`kubectl config set-credentials gokberk@gokberkgokalan.net --client-certificate=gokberkgokalan.crt --client-key=gokberkgokalan.key`

`kubectl config set-context gokberkgokalan-context --cluster=minikube --user=gokberk@gokberkgokalan.net`

`kubectl config use-context gokberkgokalan-context`

### Role Based Access Control


          apiVersion: rbac.authorization.k8s.io/v1
          kind: Role
          metadata:
            namespace: default
            name: pod-reader
          rules:
          - apiGroups: [""] # "" indicates the core API group
            resources: ["pods"] # "services", "endpoints", "pods", "pods/log" etc.
            verbs: ["get", "watch", "list"] # "get", "list", "watch", "post", "put", "create", "update", "patch", "delete"


**Role Binding**

          apiVersion: rbac.authorization.k8s.io/v1
          kind: RoleBinding
          metadata:
            name: read-pods
            namespace: default
          subjects:
          - kind: User
            name: ozgur@ozgurozturk.net # "name" is case sensitive
            apiGroup: rbac.authorization.k8s.io
          roleRef:
            kind: Role #this must be Role or ClusterRole
            name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
            apiGroup: rbac.authorization.k8s.io


**Cluster Role**

          apiVersion: rbac.authorization.k8s.io/v1
          kind: ClusterRole
          metadata:
            name: secret-reader
          rules:
          - apiGroups: [""]
            resources: ["secrets"]
            verbs: ["get", "watch", "list"]

**Cluster Role Binding**

          apiVersion: rbac.authorization.k8s.io/v1
          kind: ClusterRoleBinding
          metadata:
            name: read-secrets-global
          subjects:
          - kind: Group
            name: DevTeam # Name is case sensitive
            apiGroup: rbac.authorization.k8s.io
          roleRef:
            kind: ClusterRole
            name: secret-reader
            apiGroup: rbac.authorization.k8s.io

          

            












