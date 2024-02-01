from flask import Flask, jsonify
import mysql.connector

app = Flask(__name__)

# Configuración de la conexión a la base de datos MySQL
db_config = {
    'host': 'localhost',
    'user': 'root',
    'password': '',
    'database': 'consumer_complaints'
}

# Función para ejecutar una consulta y obtener los resultados como JSON
def execute_query(query):
    try:
        connection = mysql.connector.connect(**db_config)
        cursor = connection.cursor(dictionary=True)
        cursor.execute(query)
        results = cursor.fetchall()
        return results
    except mysql.connector.Error as err:
        print(f"Error: {err}")
        return []

# Endpoint para la consulta de quejas por producto
@app.route('/api/quejas_por_producto')
def quejas_por_producto():
    query = "SELECT ProductName, COUNT(*) AS TotalQuejas FROM complaints GROUP BY ProductName"
    results = execute_query(query)
    data = [{"ProductName": row["ProductName"], "TotalQuejas": row["TotalQuejas"]} for row in results]
    return jsonify(data)

# Endpoint para la consulta de quejas por estado
@app.route('/api/quejas_por_estado')
def quejas_por_estado():
    query = "SELECT StateName, COUNT(*) AS TotalQuejas FROM complaints GROUP BY StateName"
    results = execute_query(query)
    data = [{"StateName": row["StateName"], "TotalQuejas": row["TotalQuejas"]} for row in results]
    return jsonify(data)

# Endpoint para la consulta de principales problemas reportados
@app.route('/api/principales_problemas_reportados')
def principales_problemas_reportados():
    query = "SELECT Issue, COUNT(*) AS TotalQuejas FROM complaints GROUP BY Issue ORDER BY TotalQuejas DESC LIMIT 10"
    results = execute_query(query)
    data = [{"Issue": row["Issue"], "TotalQuejas": row["TotalQuejas"]} for row in results]
    return jsonify(data)

# Endpoint para la consulta de empresas con mayor número de quejas
@app.route('/api/empresas_con_mayor_numero_de_quejas')
def empresas_con_mayor_numero_de_quejas():
    query = "SELECT Company, COUNT(*) AS TotalQuejas FROM complaints GROUP BY Company ORDER BY TotalQuejas DESC LIMIT 10"
    results = execute_query(query)
    data = [{"Company": row["Company"], "TotalQuejas": row["TotalQuejas"]} for row in results]
    return jsonify(data)

# Endpoint para la consulta de respuestas de las empresas a los consumidores
@app.route('/api/respuestas_de_empresas_a_consumidores')
def respuestas_de_empresas_a_consumidores():
    query = "SELECT CompanyResponseToConsumer, COUNT(*) AS TotalQuejas FROM complaints GROUP BY CompanyResponseToConsumer"
    results = execute_query(query)
    data = [{"CompanyResponseToConsumer": row["CompanyResponseToConsumer"], "TotalQuejas": row["TotalQuejas"]} for row in results]
    return jsonify(data)

if __name__ == '__main__':
    app.run(debug=True)
