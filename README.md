## Juego Breack Ball
Prueba de juego con javascscript

Parte 1

Antes de que podamos programar la parte funcional del juego, donde necesite crear la estructura básica de la página que lo va a contener. Podemos hacerlo utilizando HTML y el elemento  <canvas> .
En el siguiente codigo se muestra como se creo el lienzo donde se va a mostrar el juego.
	
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Gamedev Canvas Workshop</title>
    <style>
    	* { padding: 0; margin: 0; }
    	canvas { background: #000; display: block; margin: 0 auto; }
    </style>
</head>
<body>

<canvas id="myCanvas" width="720" height="720"></canvas>

<script>
	
</script>

</body>
</html>

Parte 2

Para actualizar el dibujo del lienzo en cada fotograma, se necesito definir una función de dibujo que se ejecutará una y otra vez, cambiando una serie de variables para modificar la posición de cada personaje (sprite). Para que una misma función se ejecute una y otra vez se utulizo una función de sincronización de JavaScript, como setInterval() or requestAnimationFrame() a si como algunas variables, esto con el fin de que se crear el efecto de movimiento y poder alterar su velocidad y su paleta de color.

var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
var x = canvas.width/2;
var y = canvas.height-30;
var dx = 5;
var dy = -5;

function drawBall() {
    ctx.beginPath();
    ctx.arc(x, y, 10, 0, Math.PI*2);
    ctx.fillStyle = "red";
    ctx.fill();
    ctx.closePath();
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBall();
    x += dx;
    y += dy;
}

setInterval(draw, 10);


Parte 3

Para hacer que la pelota rebote en los cuatro bordes del Canvas se necesitaron definir las colisiones o hitbox.
Para detectar la colisión se necesita verificar si la bola está tocando (chocando con) la pared y si es así, cambiaremos la dirección de su movimiento en consecuencia.
Para facilitar los cálculos, definimos una variable llamada ballRadius que mantendrá el radio del círculo dibujado y se utilizará para los cálculos y los compara con el lienzo en el que se está trabajando con el fin de cambiar su dirección y lograr que rebote.

var ballRadius = 10;

function drawBall() {
    ctx.beginPath();
    ctx.arc(x, y, ballRadius, 0, Math.PI*2);
    ctx.fillStyle = "red";
    ctx.fill();
    ctx.closePath();
}

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBall();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy > canvas.height-ballRadius || y + dy < ballRadius) {
        dy = -dy;
    }
    
    x += dx;
    y += dy;
}

setInterval(draw, 10);


parte 4

Para poder crear interactividad se necesito crear una paleta para golpear la bola por lo que se tuvo definir variables para conseguirlo, paddleHeight servirá para definir la altura de la paleta, paddleWidth la anchura y paddleX la posición en el eje X en la que empieza a dibujarse. Definimos una función que dibujará la paleta en la pantalla. Pero hay que lograr que la paleta responda a las instrucciones del usuario por lo que se necesito crear unas funciones que lo permitan.

var paddleHeight = 10;
var paddleWidth = 75;
var paddleX = (canvas.width-paddleWidth)/2;
var rightPressed = false;
var leftPressed = false;

document.addEventListener("keydown", keyDownHandler, false);
document.addEventListener("keyup", keyUpHandler, false);

function keyDownHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = true;
    }
    else if(e.keyCode == 37) {
        leftPressed = true;
    }
}
function keyUpHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = false;
    }
    else if(e.keyCode == 37) {
        leftPressed = false;
    }
}

