<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>AREA 1 - Tienda Urbana</title>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&display=swap" rel="stylesheet">
  <style>
    body { margin:0; font-family:'Orbitron', sans-serif; background:#111; color:#eee; }
    header { text-align:center; padding:2rem; }
    header h1 { margin:0; font-size:2.5rem; letter-spacing:3px; }
    nav { display:flex; justify-content:center; gap:1rem; background:#222; padding:.75rem; position:sticky; top:0; }
    nav button { background:transparent; border:1px solid #333; color:#eee; padding:.5rem .75rem; border-radius:6px; cursor:pointer; }
    nav button.active, nav button:hover { border-color:#f00; color:#f00; }
    .catalogo { display:grid; grid-template-columns:repeat(auto-fit,minmax(240px,1fr)); gap:1rem; padding:1.5rem; }
    .producto { background:#1a1a1a; border:1px solid #333; border-radius:10px; overflow:hidden; display:flex; flex-direction:column; }
    .producto img { width:100%; height:240px; object-fit:cover; }
    .contenido { padding:.75rem 1rem; }
    .acciones { padding:.75rem 1rem; display:flex; flex-direction:column; gap:.5rem; }
    select { padding:.4rem; border-radius:6px; border:1px solid #333; background:#0e0e0e; color:#fff; }
    .btn { border:none; cursor:pointer; border-radius:6px; padding:.5rem .75rem; }
    .btn-primary { background:#f00; color:#fff; }
    .btn-primary:hover { background:#c00; }
    #carrito { position:fixed; right:0; top:0; width:360px; height:100%; background:#111; border-left:2px solid #444; padding:1rem; display:none; overflow-y:auto; }
    #carrito h2 { color:#f00; }
    #carrito ul { list-style:none; padding:0; }
    #toggleCarrito { position:fixed; bottom:20px; right:20px; background:#f00; color:#fff; border:none; padding:1rem; border-radius:50%; cursor:pointer; }
  </style>
</head>
<body>
  <header><h1>AREA 1</h1></header>
  <nav>
    <button data-cat="promos" class="active">PROMOS %Off</button>
    <button data-cat="remeras">Remeras</button>
    <button data-cat="pantalones">Pantalones</button>
    <button data-cat="camperas">Camperas</button>
    <button data-cat="accesorios">Accesorios</button>
  </nav>

  <section class="catalogo" id="catalogo"></section>

  <div id="carrito">
    <h2>Carrito</h2>
    <ul id="listaCarrito"></ul>
    <p id="total"></p>
    <button class="btn btn-primary" id="checkoutBtn">Iniciar compra (WhatsApp)</button>
  </div>
  <button id="toggleCarrito">🛒</button>

  <script>
    const whatsAppNumber = '5491138453507';
    let carrito = [];
    let catalogo = [
      {id:'1', nombre:'Remera Negra', categoria:'remeras', precio:12000, talles:{S:5,M:3,L:0,XL:2}, descripcion:'Algodón 100%', imagen:'img/remeras/remera-negra.jpg'},
      {id:'2', nombre:'Campera Urbana', categoria:'camperas', precio:25000, talles:{M:2,L:2,XL:1}, descripcion:'Corte oversize', imagen:'img/camperas/campera-negra.jpg'}
    ];
    let currentCat='promos';

    function renderCatalogo(cat='promos'){
      const cont=document.getElementById('catalogo');
      cont.innerHTML='';
      catalogo.filter(p=>cat==='promos'?true:p.categoria===cat).forEach(p=>{
        const card=document.createElement('div');
        card.className='producto';
        let opciones='';
        for(const [talle,stock] of Object.entries(p.talles)){
          opciones+=`<option value="${talle}" ${stock<=0?'disabled':''}>${talle} ${stock<=0?'(Sin stock)':''}</option>`;
        }
        card.innerHTML=`
          <img src="${p.imagen}" alt="${p.nombre}">
          <div class="contenido">
            <h3>${p.nombre}</h3>
            <p>ARS ${p.precio}</p>
          </div>
          <div class="acciones">
            <select id="sel-${p.id}">${opciones}</select>
            <button class="btn btn-primary" data-id="${p.id}">Agregar</button>
          </div>`;
        cont.appendChild(card);
      });
      document.querySelectorAll('button[data-id]').forEach(btn=>{
        btn.onclick=()=>{
          const id=btn.getAttribute('data-id');
          const prod=catalogo.find(x=>x.id===id);
          const talle=document.getElementById('sel-'+id).value;
          if(prod.talles[talle]<=0){alert('Sin stock en este talle');return;}
          carrito.push({nombre:prod.nombre, precio:prod.precio, talle});
          renderCarrito();
        };
      });
    }

    function renderCarrito(){
      const lista=document.getElementById('listaCarrito');
      lista.innerHTML='';
      let total=0;
      carrito.forEach(item=>{
        const li=document.createElement('li');
        li.textContent=`${item.nombre} - Talle ${item.talle} - ARS ${item.precio}`;
        lista.appendChild(li);
        total+=item.precio;
      });
      document.getElementById('total').textContent=`Total: ARS ${total}`;
    }

    document.getElementById('toggleCarrito').onclick=()=>{
      const c=document.getElementById('carrito');
      c.style.display=c.style.display==='block'?'none':'block';
    };

    document.getElementById('checkoutBtn').onclick=()=>{
      if(carrito.length===0){alert('Carrito vacío');return;}
      let mensaje=`Pedido AREA 1%0A%0AProductos:%0A`;
      carrito.forEach(item=>{
        mensaje+=`- ${item.nombre} Talle ${item.talle} ARS ${item.precio}%0A`;
      });
      const url=`https://wa.me/${whatsAppNumber}?text=${mensaje}`;
      window.open(url,'_blank');
    };

    document.querySelectorAll('nav button').forEach(btn=>{
      btn.onclick=()=>{
        document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active'));
        btn.classList.add('active');
        currentCat=btn.getAttribute('data-cat');
        renderCatalogo(currentCat);
      };
    });

    renderCatalogo(currentCat);
  </script>
</body>
</html>
