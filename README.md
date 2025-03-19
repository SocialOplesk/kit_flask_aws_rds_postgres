# SOCIAL OPLESK
### 🎩 KIT 
<br/>

## ⚡️ Kit de código fuente para la elaboración del CRUD de Flask + RDS ⚡️

<br/> 

## 🏆 K-1 (CÓDIGO FUENTE POSTGRESQL)
Crear la tabla de usuarios en AWS RDS mediante PG-ADMIN.
```
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    age INTEGER,
    city VARCHAR(100)
);
```


## 🏆 K-2 (CÓDIGO FUENTE POSTGRESQL)
2. Insertar 3 usuarios en la tabla users
```
INSERT INTO users (name, email, age, city) VALUES
('Juan Pérez', 'juan.perez@example.com', 30, 'Madrid'),
('María López', 'maria.lopez@example.com', 25, 'Barcelona'),
('Carlos García', 'carlos.garcia@example.com', 35, 'Valencia');
``` 

## 🏆 K-3 (CREAR PROYECTO EN TÚ COMPUTADOR LOCAL)
crear el archivo .env en el root del proyecto
```
DB_USERNAME=usuario de postgres
DB_PASSWORD=contraseña del postgres
DB_HOST=dirección de puerta de enlace
DB_NAME=nomb re de la base de datos
SECRET_KEY=tu_clave_secreta
```

## 🏆 K-4 (CÓDIGO FUENTE FLASK)
4. Crea el archivo app.py con el siguiente código:

```
from flask import Flask, request, jsonify
import psycopg2
import os

app = Flask(__name__)

# Database connection
def get_db_connection():
    conn = psycopg2.connect(
        host=os.environ['DB_HOST'],
        database=os.environ['DB_NAME'],
        user=os.environ['DB_USERNAME'],
        password=os.environ['DB_PASSWORD']
    )
    return conn


# Create
@app.route('/users', methods=['POST'])
def create_user():
    user = request.json
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('INSERT INTO users (name, email, age, city) VALUES (%s, %s, %s, %s) RETURNING id',
                (user['name'], user['email'], user['age'], user['city']))
    user_id = cur.fetchone()[0]
    conn.commit()
    cur.close()
    conn.close()
    return jsonify({'id': user_id, 'message': 'User created successfully'}), 201


# Read all
@app.route('/users', methods=['GET'])
def get_users():
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('SELECT * FROM users')
    users = cur.fetchall()
    cur.close()
    conn.close()
    return jsonify([{'id': user[0], 'name': user[1], 'email': user[2], 'age': user[3], 'city': user[4]} for user in users])


# Read one
@app.route('/users/<int:id>', methods=['GET'])
def get_user(id):
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('SELECT * FROM users WHERE id = %s', (id,))
    user = cur.fetchone()
    cur.close()
    conn.close()
    if user:
        return jsonify({'id': user[0], 'name': user[1], 'email': user[2], 'age': user[3], 'city': user[4]})
    return jsonify({'message': 'User not found'}), 404


# Update
@app.route('/users/<int:id>', methods=['PUT'])
def update_user(id):
    user = request.json
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('UPDATE users SET name = %s, email = %s, age = %s, city = %s WHERE id = %s',
                (user['name'], user['email'], user['age'], user['city'], id))
    updated = cur.rowcount > 0
    conn.commit()
    cur.close()
    conn.close()
    if updated:
        return jsonify({'message': 'User updated successfully'})
    return jsonify({'message': 'User not found'}), 404

# Delete
@app.route('/users/<int:id>', methods=['DELETE'])
def delete_user(id):
    conn = get_db_connection()
    cur = conn.cursor()
    cur.execute('DELETE FROM users WHERE id = %s', (id,))
    deleted = cur.rowcount > 0
    conn.commit()
    cur.close()
    conn.close()
    if deleted:
        return jsonify({'message': 'User deleted successfully'})
    return jsonify({'message': 'User not found'}), 404

if __name__ == '__main__':
    app.run(debug=True)
```


---
<h3 align="center">SOCIAL OPLESK</h3>
