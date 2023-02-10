apiVersion: v1
kind: Secret
metadata:
  name: my-app-db-secret
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: <base64 encoded root password>
  MYSQL_DATABASE: <base64 encoded database name>
  MYSQL_USER: <base64 encoded user name>
  MYSQL_PASSWORD: <base64 encoded password>





apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app-image:v1
        ports:
        - containerPort: 80
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_DATABASE
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_PASSWORD
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-db
spec:
  selector:
    app: my-app-db
  ports:
  - name: db
    port: 3306
    targetPort: 3306
  clusterIP: None
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-db-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app-db
  template:
    metadata:
      labels:
        app: my-app-db
    spec:
      containers:
      - name: my-app-db
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: my-





new
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-app-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /data/my-app-pv

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-app-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app-image:v1
        ports:
        - containerPort: 80
        volumeMounts:
        - name: my-app-data
          mountPath: /var/lib/mysql
      volumes:
      - name: my-app-data
        persistentVolumeClaim:
          claimName: my-app-pvc

---
apiVersion: v1
kind: Service
metadata:
  name: my-app-db
spec:
  selector:
    app: my-app-db
  ports:
  - name: db
    port: 3306
    targetPort: 3306
  clusterIP: None

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-db-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app-db
  template:
    metadata:
      labels:
        app: my-app-db
    spec:
      containers:
      - name: my-app-db
        image: mysql:5.7
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_DATABASE
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: my-app-db-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-app-db
