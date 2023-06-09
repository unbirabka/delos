# delos interview

name: akbar ibnu abdillah

blog: https://sekolahlinux.com

linkedin: https://www.linkedin.com/in/ibnuakbar/
#
# layout tree directory

```
.
├── .github
│   └── workflows
│       └── service-a.yml
├── README.md
└── service-a
    ├── Dockerfile
    ├── main.py
    ├── requirements.txt
    └── values.yaml.bak

```
#
# helm repository
disi saya menggunakan helm repository yang saya buat sendiri di github account saya, berikut ini github repository helm yang saya gunakan jika ingin melihat detailnya

- https://github.com/unbirabka/helm-sekolahlinux

lalu untuk add helm repository nya bisa dengan perintah dibawah
```
helm repo add sekolahlinux https://unbirabka.github.io/helm-sekolahlinux/charts
helm repo update
```

#
# ci/cd

pada ci/cd ini saya membaginya menjadi 3 step, bisa dilihat di `.github/workflows/service-a.yml

- lint test
- docker build image & push to docker hub registry
- deploy to k8s with helm


pada bagian `deploy to k8s with helm` karena disini saya tidak terhubung ke cluster eks k8s yang sebenarnya jadi beberapa code saya command, dan saya ganti dengan command untuk generate dibawah ini

```
helm template service-a sekolahlinux/sekolahlinux --version=0.1.0 -f service-a/values.yaml > service-a-${{env.SHORT_SHA}}.yaml
```

lalu kemudian manifest yang telah di generate saya artifact untuk bisa di download dengan command dibawah ini

```
 - name: 'Upload Artifact'
   uses: actions/upload-artifact@v3
   with:
     name: artifact manifest k8s service-a
     path: service-a-${{env.SHORT_SHA}}.yaml
     retention-days: 3
```
#
# k8s manifest

pada manifest yang di generate saya menggunakan beberapa module k8s seperti

- service
- deployment
- pods
- hpa
- ingress (terintegrasi dengan ingress-nginx)

untuk konfigurasi yang mengekspose service ke public saya menggunakan ingress dan bisa dilihat pada values.yaml.bak, contohnya seperti dibawah

```
ingress: 
  enabled: true
  annotations: {}
  
  class: nginx
  issuer:
    namespaced: false 
    name: letsencrypt

  tls:
    - hosts:
        - service-a.sekolahlinux.com
    
  hosts:
    - host: service-a.sekolahlinux.com
      paths:
        - "/" 
  pathtype: ImplementationSpecific

```
