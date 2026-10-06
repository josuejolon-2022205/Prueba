spring.application.name=spring
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/prueba_spring_in5cm?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=America/Guatemala&characterEncoding=utf8
spring.datasource.username=root
spring.datasource.password=1234
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    color: #333;
}

nav {
    background-color: #2c3e50;
    padding: 12px 24px;
    display: flex;
    gap: 16px;
}

nav a {
    color: #fff;
    text-decoration: none;
    font-size: 15px;
    padding: 6px 12px;
    border-radius: 4px;
    transition: background 0.2s;
}

.container {
    max-width: 960px;
    margin: 32px auto;
    padding: 0 16px;
}

h2 {
    margin-bottom: 20px;
    color: #2c3e50;
    border-bottom: 2px solid #2c3e50;
    padding-bottom: 8px;
}

.form-card {
    background: #fff;
    border-radius: 8px;
    padding: 24px;
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
    margin-bottom: 32px;
}

.form-group {
    margin-bottom: 16px;
}

.form-group label {
    display: block;
    margin-bottom: 6px;
    font-weight: bold;
    font-size: 14px;
}

.form-group input,
.form-group select,
.form-group textarea {
    width: 100%;
    padding: 8px 12px;
    border: 1px solid #ccc;
    border-radius: 4px;
    font-size: 14px;
}

.form-group textarea {
    resize: vertical;
    min-height: 80px;
}

.btn {
    padding: 8px 18px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 14px;
    text-decoration: none;
    display: inline-block;
}

.btn-primary   { background-color: #2980b9; color: #fff; }
.btn-warning   { background-color: #f39c12; color: #fff; }
.btn-danger    { background-color: #e74c3c; color: #fff; }
.btn-secondary { background-color: #358a91; color: #fff; }

.btn:hover { opacity: 0.85; }

table {
    width: 100%;
    border-collapse: collapse;
    background: #fff;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 6px rgba(0,0,0,0.1);
}

thead {
    background-color: #2c3e50;
    color: #fff;
}

th, td {
    padding: 12px 16px;
    text-align: left;
    font-size: 14px;
    border-bottom: 1px solid #eee;
}

tbody tr:hover {
    background-color: #f0f4f8;
}

.acciones {
    display: flex;
    gap: 8px;
}




package com.pruebaTec.spring.Controllers;

import com.pruebaTec.spring.Service.AutorService;
import com.pruebaTec.spring.entity.Autor;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/api/autor")
public class AutorController {
    private final AutorService autorService;

    public AutorController(AutorService autorService) {
        this.autorService = autorService;
    }

    @GetMapping
    public String listarAutor(Model model){
        model.addAttribute("autor", autorService.getAllAutor());
        model.addAttribute("autorFormu", new Autor());
        return "autor";
    }

    @GetMapping("/editarAutor/{id}")
    public String editarAutor(@PathVariable Integer id, Model model){
        model.addAttribute("autor", autorService.getAllAutor());
        model.addAttribute("autorFormu", autorService.getById(id));
        return "autor";
    }

    @GetMapping("/buscarAutor")
    public String buscarAutor(@RequestParam Integer id, Model model){
        model.addAttribute("autor", autorService.getAllAutor());
        model.addAttribute("autorFormu", autorService.getById(id));
        return "autor";
    }

    @PostMapping
    public String crearAutor(@ModelAttribute("autorFormu") Autor autor){
        autorService.saveAutor(autor);
        return "redirect:/autor";
    }

    @PostMapping("/actualizarAutor/{id}")
    public String actualizarAutor(@PathVariable Integer id, @ModelAttribute("autorFormu") Autor autor){
        autorService.updateAutor(id, autor);
        return "redirect:/autor";
    }

    @PostMapping("/eliminarAutor/{id}")
    public String eliminarAutor(@PathVariable Integer id){
        autorService.deleteAutor(id);
        return "redirect:/autor";
    }
}

import { Pool, PoolConfig } from "pg";
import dotenv from "dotenv";

dotenv.config();

const useSsl = (process.env.DB_SSL ?? "").toLowerCase() === "true";

const config: PoolConfig = process.env.DATABASE_URL
    ? {
        connectionString: process.env.DATABASE_URL,
        ssl: useSsl ? { rejectUnauthorized: false } : undefined,
    }
    : {
        host: process.env.HOST,
        port: Number(process.env.DB_PORT ?? process.env.PORT ?? 5432),
        user: process.env.USER,
        password: process.env.PASSWORD,
        database: process.env.DB,
        ssl: useSsl ? { rejectUnauthorized: false } : undefined,
    };

export const pool = new Pool(config);

export async function pruebaConexion() {
    try {
        await pool.query("SELECT NOW()");
        console.log("Conexión a PostgreSQL exitosa");
    } catch (err) {
        console.error("No se pudo conectar a PostgreSQL", err);
        throw err;
    }
}

{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "rootDir": "./src",
    "outDir": "./dist",
    "strict": true,
    "types": ["node"]
  }
}

<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Vehiculos</title>
    <link rel="stylesheet" th:href="@{/Style.css}">
</head>
<body>

<nav>
    <a th:href="@{/Cliente}">cliente</a>
    <a th:href="@{/Compra}">compra</a>
    <a th:href="@{/Revision}">revision</a>
    <a th:href="@{/Vehiculo}">vehiculo</a>
</nav>

<div class="container">

    <div class="form-card">
        <h2 th:text="${vehiculoFormu.matricula != null} ? 'Editar vehicula' : 'Nuevo vehicula'">Libro</h2>

        <form th:action="${vehiculoFormu.matricula != null}
                         ? @{/vehiculo/actualizarVehiculo/{id}(id=${vehiculoFormu.matricula})}
                         : @{/vehiculo/guardarVehiculo}"
              th:object="${vehiculoFormu}"
              method="post">

            <input type="hidden" th:field="*{matricula}">

            <div class="form-group">
                <label>Marca</label>
                <input type="text" th:field="*{marca}" >
            </div>

            <div class="form-group">
                <label>Modelo</label>
                <input type="text" th:field="*{modelo}" >
            </div>

            <div class="form-group">
                <label>color</label>
                <input type="text" th:field="*{color}" >
            </div>

            <div class="form-group">
                <label>Precio</label>
                <input type="number" th:field="*{precio}">
            </div>


            <button type="submit" class="btn btn-primary"
                    th:text="${vehiculoFormu.matricula != null} ? 'Actualizar' : 'Guardar'">
                Guardar
            </button>

            <a th:href="@{/vehiculo}" class="btn btn-secondary">Cancelar</a>
        </form>
    </div>

    <h2>Listado de Vehiculos</h2>
    <table>
        <thead>
            <tr>
                <th>ID</th>
                <th>Marca</th>
                <th>Modelo</th>
                <th>Color</th>
                <th>Precio</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody>
            <tr th:each="v : ${vehiculo}">
                <td th:text="${v.matricula}"></td>
                <td th:text="${v.marca}"></td>
                <td th:text="${v.modelo}"></td>
                <td th:text="${v.color}"></td>
                <td th:number="${v.precio}"></td>>
                <td class="acciones">
                    <a th:href="@{/vehiculo/editarVehiculo/{id}(id=${v.matricula})}"
                       class="btn btn-warning">Editar</a>

                    <form th:action="@{/vehiculo/eliminarVehiculo/{id}(id=${v.matricula})}"
                          method="post"
                          onsubmit="return confirm('¿Eliminar este vehiculo?')">
                        <button type="submit" class="btn btn-danger">Eliminar</button>
                    </form>
                </td>
            </tr>
            <tr th:if="${#lists.isEmpty(vehiculo)}">
                <td colspan="6" style="text-align:center; color:#999;">No hay vehiculos registrados.</td>
            </tr>
        </tbody>
    </table>

</div>

</body>
</html>

-- OBTENER TODAS
SELECT *
FROM Valoraciones
ORDER BY id_valoracion DESC;


-- CREAR
INSERT INTO Valoraciones (
    id_servicio,
    id_usuario,
    comentario,
    calificacion
)
VALUES ($1, $2, NULLIF(TRIM($3), ''), $4)
RETURNING ;


-- BUSCAR POR ID
SELECT
FROM Valoraciones
WHERE id_valoracion = $1;


-- EDITAR
UPDATE Valoraciones
SET id_servicio = $1,
    id_usuario = $2,
    comentario = NULLIF(TRIM($3), ''),
    calificacion = $4
WHERE id_valoracion = $5
RETURNING *;


-- ELIMINAR
DELETE FROM Valoraciones
WHERE id_valoracion = $1;

import { Request, Response, NextFunction } from "express";
import { listarProveedores, buscarProveedor, agregarProveedor, actualizarProveedor, eliminarProveedor } from "../services/proveedores.service";
import { Proveedores } from "../models/Proveedores";

export async function obtenerProveedores(_req: Request, res: Response, next: NextFunction) {
    try {
        const proveedores = await listarProveedores();
        return res.status(200).json({
            success: true,
            message: "Proveedores cargados correctamente",
            data: proveedores
        });
    } catch (error) {
        next(error);
    }
}

export async function obtenerProveedorPorId(req: Request, res: Response, next: NextFunction) {
    try {
        const id = Number(req.params.id);
        const proveedor = await buscarProveedor(id);

        return res.status(200).json({
            success: true,
            message: `Proveedor con id: ${id} encontrado`,
            data: proveedor
        });
    } catch (error) {
        next(error);
    }
}

export async function crearProveedor(req: Request, res: Response, next: NextFunction) {
    try {
        const { id_usuario, nombre_negocio, direccion, telefono_contacto } = req.body;
        const newProveedor: Proveedores = { id_usuario, nombre_negocio, direccion, telefono_contacto };

        const proveedorCreado = await agregarProveedor(newProveedor);
        return res.status(201).json({
            success: true,
            message: 'Proveedor creado',
            data: proveedorCreado
        });
    } catch (error) {
        next(error);
    }
}

export async function editarProveedor(req: Request, res: Response, next: NextFunction) {
    try {
        const id = Number(req.params.id);
        const { id_usuario, nombre_negocio, direccion, telefono_contacto } = req.body;
        const newProveedor: Proveedores = { id_usuario, nombre_negocio, direccion, telefono_contacto };

        const proveedorEditado = await actualizarProveedor(id, newProveedor);
        return res.status(200).json({
            success: true,
            message: `Proveedor con id: ${id} editado`,
            data: proveedorEditado
        });
    } catch (error) {
        next(error);
    }
}

export async function eliminarProveedores(req: Request, res: Response, next: NextFunction) {
    try {
        const id = Number(req.params.id);
        const resultado = await eliminarProveedor(id);

        return res.status(200).json({
            success: true,
            message: `Proveedor con id: ${id} eliminado`,
            data: resultado
        });
    } catch (error) {
        next(error);
    }
}

import { pool } from "../config/conexion";
import { NotFoundError } from "../errors/notFound.error";
import { Proveedores } from "../models/Proveedores";
import { errorThrower } from "../utils/middleware/errorThrower";

export async function listarProveedores(){
    try{
        const consulta = await pool.query("select * from sp_proveedores_obtener()");
        return consulta.rows;
    }catch(error){
        errorThrower(error)
    }
}

export async function agregarProveedor(prov: Proveedores){
    try{
        const values = [prov.id_usuario, prov.nombre_negocio, prov.direccion, prov.telefono_contacto]
        const consulta = "select * from sp_proveedores_crear($1, $2, $3, $4)"
        const resultado = await pool.query(consulta, values)
        return resultado.rows[0];
    }catch(error){
        errorThrower(error)
    }
}

export async function buscarProveedor(id: number){
    try{
        const resultado = await pool.query("select * from sp_proveedores_buscar($1)", [id])

        if(!resultado.rows[0]){
            throw new NotFoundError(`el id del proveedor ${id} no se encontro`)
        }
        return resultado.rows[0]
    }catch(error){
        errorThrower(error)
    }
}

export async function actualizarProveedor(id: number, prov: Proveedores){
    try{
        const values = [id, prov.id_usuario, prov.nombre_negocio, prov.direccion, prov.telefono_contacto]
        const consulta = "select * from sp_proveedores_editar($1, $2, $3, $4, $5)"
        const resultado = await pool.query(consulta, values)

        if(!resultado.rows[0]){
            throw new NotFoundError(`no se pudo editar el proveedor porque el id ${id} no existe`)
        }

        return resultado.rows[0];
    }catch(error){
        errorThrower(error)
    }
}

export async function eliminarProveedor(id: number){
    try{
        const consulta = await pool.query("select sp_proveedores_eliminar($1)", [id]);
        
        if (!consulta.rows[0].eliminado){
            throw new NotFoundError("no se pudo eliminar el proveedor porque el id no existe")
        }

        return true
    }catch(error){
        errorThrower(error)
    }
}
