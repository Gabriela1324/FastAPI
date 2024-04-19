# FastAPI
creacion de api con fastapi

#main.py
from fastapi import FastAPI
from Usuarios import router as usuarios_router
from Productos import router as productos_router
from Pedidos import router as pedidos_router

app = FastAPI()

app.include_router(usuarios_router, prefix="/usuarios",tags=["usuarios"])
app.include_router(productos_router,prefix="/productos", tags=["productos"])
app.include_router(pedidos_router,prefix="/pedidos", tags=["pedidos"] )

#models.py
from sqlalchemy import Column, Integer, String, Float, Date
from sqlalchemy.ext.declarative import declarative_base
import sys
Base = declarative_base()

class Usuario(Base):
  
    __tablename__ = "Usuarios"
    ID = Column(Integer, primary_key=True, index=True)
    Nombre = Column(String(50))
    Edad = Column(Integer)

class Producto(Base):
    __tablename__ = "Productos"
    ID = Column(Integer, primary_key=True, index=True)
    Nombre = Column(String(100))
    Precio = Column(Float)

class Pedido(Base):
    __tablename__ = "Pedidos"
    ID = Column(Integer, primary_key=True, index=True)
    Fecha = Column(Date)
    UsuarioID = Column(Integer)
    ProductoID = Column(Integer)
    Cantidad = Column(Integer)

    #Usuarios.py
    # usuarios.py
from fastapi import APIRouter, HTTPException, Depends
from sqlalchemy.orm import Session
from dataBase import get_session
from models import Usuario, UsuarioCreate, UsuarioUpdate
from dataBase import engine
router = APIRouter()

# Crear un usuario
@router.post("/usuarios/", response_model=Usuario)
def create_usuario(usuario: UsuarioCreate, db: Session = Depends(get_session)):
    db_usuario = Usuario(**usuario.dict())
    db.add(db_usuario)
    db.commit()
    db.refresh(db_usuario)
    return db_usuario

# Obtener un usuario por ID
@router.get("/usuarios/{usuario_id}", response_model=Usuario)
def read_usuario(usuario_id: int, db: Session = Depends(get_session)):
    usuario = db.query(Usuario).filter(Usuario.ID == usuario_id).first()
    if usuario is None:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    return usuario

# Actualizar un usuario por ID
@router.put("/usuarios/{usuario_id}", response_model=Usuario)
def update_usuario(usuario_id: int, usuario: UsuarioUpdate, db: Session = Depends(get_session)):
    db_usuario = db.query(Usuario).filter(Usuario.ID == usuario_id).first()
    if db_usuario is None:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    for attr, value in usuario.dict().items():
        setattr(db_usuario, attr, value)
    db.commit()
    db.refresh(db_usuario)
    return db_usuario

# Eliminar un usuario por ID
@router.delete("/usuarios/{usuario_id}")
def delete_usuario(usuario_id: int, db: Session = Depends(get_session)):
    db_usuario = db.query(Usuario).filter(Usuario.ID == usuario_id).first()
    if db_usuario is None:
        raise HTTPException(status_code=404, detail="Usuario no encontrado")
    db.delete(db_usuario)
    db.commit()
    return {"mensaje": "Usuario eliminado"}

    #productos.py 
# productos.py
from fastapi import APIRouter, HTTPException, Depends
from sqlalchemy.orm import Session
from dataBase import get_session
from models import Producto, ProductoCreate, ProductoUpdate

router = APIRouter()

# Crear un producto
@router.post("/productos/", response_model=Producto)
def create_producto(producto: ProductoCreate, db: Session = Depends(get_session)):
    db_producto = Producto(**producto.dict())
    db.add(db_producto)
    db.commit()
    db.refresh(db_producto)
    return db_producto

# Obtener un producto por ID
@router.get("/productos/{producto_id}", response_model=Producto)
def read_producto(producto_id: int, db: Session = Depends(get_session)):
    producto = db.query(Producto).filter(Producto.ID == producto_id).first()
    if producto is None:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    return producto

# Actualizar un producto por ID
@router.put("/productos/{producto_id}", response_model=Producto)
def update_producto(producto_id: int, producto: ProductoUpdate, db: Session = Depends(get_session)):
    db_producto = db.query(Producto).filter(Producto.ID == producto_id).first()
    if db_producto is None:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    for attr, value in producto.dict().items():
        setattr(db_producto, attr, value)
    db.commit()
    db.refresh(db_producto)
    return db_producto

# Eliminar un producto por ID
@router.delete("/productos/{producto_id}")
def delete_producto(producto_id: int, db: Session = Depends(get_session)):
    db_producto = db.query(Producto).filter(Producto.ID == producto_id).first()
    if db_producto is None:
        raise HTTPException(status_code=404, detail="Producto no encontrado")
    db.delete(db_producto)
    db.commit()
    return {"mensaje": "Producto eliminado"}
#Pedidos.py
from fastapi import APIRouter, HTTPException, Depends
from sqlalchemy.orm import Session
from dataBase import get_session
from models import Pedido, PedidoCreate, PedidoUpdate

router = APIRouter()

# Crear un pedido
@router.post("/pedidos/", response_model=Pedido)
def create_pedido(pedido: PedidoCreate, db: Session = Depends(get_session)):
    db_pedido = Pedido(**pedido.dict())
    db.add(db_pedido)
    db.commit()
    db.refresh(db_pedido)
    return db_pedido

# Obtener un pedido por ID
@router.get("/pedidos/{pedido_id}", response_model=Pedido)
def read_pedido(pedido_id: int, db: Session = Depends(get_session)):
    pedido = db.query(Pedido).filter(Pedido.ID == pedido_id).first()
    if pedido is None:
        raise HTTPException(status_code=404, detail="Pedido no encontrado")
    return pedido

# Actualizar un pedido por ID
@router.put("/pedidos/{pedido_id}", response_model=Pedido)
def update_pedido(pedido_id: int, pedido: PedidoUpdate, db: Session = Depends(get_session)):
    db_pedido = db.query(Pedido).filter(Pedido.ID == pedido_id).first()
    if db_pedido is None:
        raise HTTPException(status_code=404, detail="Pedido no encontrado")
    for attr, value in pedido.dict().items():
        setattr(db_pedido, attr, value)
    db.commit()
    db.refresh(db_pedido)
    return db_pedido

# Eliminar un pedido por ID
@router.delete("/pedidos/{pedido_id}")
def delete_pedido(pedido_id: int, db: Session = Depends(get_session)):
    db_pedido = db.query(Pedido).filter(Pedido.ID == pedido_id).first()
    if db_pedido is None:
        raise HTTPException(status_code=404, detail="Pedido no encontrado")
    db.delete(db_pedido)
    db.commit()
    return {"mensaje": "Pedido eliminado"}
#DataBase.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

DATABASE_URL = "mysql+pymysql://root:1026551311@localhost/revista"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def get_session():
    dataBase = SessionLocal()
    try:
        yield dataBase
    finally:
        dataBase.close()

    
