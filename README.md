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
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBall();
    drawPaddle();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy > canvas.height-ballRadius || y + dy < ballRadius) {
        dy = -dy;
    }
    
    if(rightPressed && paddleX < canvas.width-paddleWidth) {
        paddleX += 7;
    }
    else if(leftPressed && paddleX > 0) {
        paddleX -= 7;
    }
    
    x += dx;
    y += dy;
}

setInterval(draw, 10);
 
 Parte 5
 
Ahora hay se implemento un fin del juego en nuestro codigo por lo que se tuvo que definir las condiciones en las cuales este evento ocurre y en el caso de este juego seria cuando la pelota toque la parte inferior.

function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBall();
    drawPaddle();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy < ballRadius) {
        dy = -dy;
    }
    else if(y + dy > canvas.height-ballRadius) {
        if(x > paddleX && x < paddleX + paddleWidth) {
            dy = -dy;
        }
        else {
            alert("Perdiste");
            document.location.reload();
        }
    }
    
    if(rightPressed && paddleX < canvas.width-paddleWidth) {
        paddleX += 7;
    }
    else if(leftPressed && paddleX > 0) {
        paddleX -= 7;
    }
    
    x += dx;
    y += dy;
}

setInterval(draw, 10);

Parte 6

Como el objetivo del juego es romper ladrillos con la bola es necesario que crearlos. Podríamos dibujar los ladrillos uno a uno o podemos usar lo aprendido hasta ahora y simplemente crear una función para que el programa nos los dibuje por nosotros, inclusive podemos crear una variable para que el programa nos seleccione el color de los ladrillos por nosotros.

var brickRowCount = 7;
var brickColumnCount = 8;
var brickWidth = 75;
var brickHeight = 20;
var brickPadding = 10;
var brickOffsetTop = 30;
var brickOffsetLeft = 30;
var colorR = 'rgb(' + (Math.floor(Math.random() * 256)) + ',' + (Math.floor(Math.random() * 256)) + ',' + (Math.floor(Math.random() * 256)) + ')';

var bricks = [];
for(c=0; c<brickColumnCount; c++) {
    bricks[c] = [];
    for(r=0; r<brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0 };
    }
}
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBricks();
    drawBall();
    drawPaddle();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy < ballRadius) {
        dy = -dy;
    }
    else if(y + dy > canvas.height-ballRadius) {
        if(x > paddleX && x < paddleX + paddleWidth) {
           if(y= y-paddleHeight){
            dy = -dy  ;
			 }
        }
        else {
            alert("Perdiste");
            document.location.reload();
        }
    }
    
    if(rightPressed && paddleX < canvas.width-paddleWidth) {
        paddleX += 7;
    }
    else if(leftPressed && paddleX > 0) {
        paddleX -= 7;
    }
    
    x += dx;
    y += dy;
}

setInterval(draw, 10);

Parte 7

Para lograr que la pelota interactue con los ladrillos se necesito a definir una función que, con un bucle, recorrerá todos los ladrillos y comparará la posición de cada uno con la posición de la bola, cada vez que se dibuje un fotograma, esto con el fin de poder identificar las colisiones de los ladrillos y la bola pueda interactuar con los mismos.

function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                }
            }
        }
    }
}
function draw(){
	ctx.clearRect(0,0,canvas.width,canvas.height);
	drawBricks();
	drawBall();
	drawPaddle();
	collisionDetection();
 if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy < ballRadius) {
        dy = -dy;
    }
    else if(y + dy > canvas.height-ballRadius) {
        if(x > paddleX && x < paddleX + paddleWidth) {
			 if(y= y-paddleHeight){
            dy = -dy  ;
			 }
        }
        else {
            alert("Perdiste");
            document.location.reload();
        }
    }
	if(rightPressed && paddleX<canvas.width-paddleWidth){
		
		paddleX+=7;
		}
	 else if(leftPressed && paddleX>0 ){
		 paddleX-=7;
		 
		 }
		 
		 x=x+dx;
	     y=y+dy;
	}

setInterval(draw,10);

Parte 8

Ahora que tenemos las colisiones podemos aplicar los contadores para llevar el conteo de los puntos que llevamos y utilizando lo que hemos aprendido hasta ahora podemos crear una función que nos muestre el puntaje con la ayuda de la función de colisiones que hicimos para que el programa sepa cuando hizo un punto y gracias a que llevamos un conteo de los puntos ya podremos definir las condiciones de victoria para el jugador teniendo en cuenta los puntajes.

function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                    score++;
                    if(score == brickRowCount*brickColumnCount) {
                        alert("Ganaste");
                        document.location.reload();
                    }
                }
            }
        }
    }
}
function drawScore() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#0095DD";
    ctx.fillText("Score: "+score, 8, 20);
}

Parte 9 

Ya que se quizo pulir un poco más el juego se implemento una forma de cambiar la posición de la pala basándonos en las coordenadas del puntero del ratón haciendo una función similar a la de la detención de botones, pero ahora teniendo en cuenta las coordenadas del ratón. 

function mouseMoveHandler(e) {
    var relativeX = e.clientX - canvas.offsetLeft;
    if(relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth/2;
    }
  }
  
Parte 10

Para terminar, Se decodio dar vidas al jugador para evitar que el juego termine muy rápido y darle más oportunidades al jugador de fallar y así definir las condiciones de fin del juego de acurdo con estos nuevos datos.

function drawLives() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#0095DD";
    ctx.fillText("Lives: "+lives, canvas.width-65, 20);
}
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawBricks();
    drawBall();
    drawPaddle();
    drawScore();
    drawLives();
    collisionDetection();
    
    if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
        dx = -dx;
    }
    if(y + dy < ballRadius) {
        dy = -dy;
    }
    else if(y + dy > canvas.height-ballRadius) {
        if(x > paddleX && x < paddleX + paddleWidth) {
            dy = -dy;
        }
        else {
            lives--;
            if(!lives) {
                alert("Perdiste");
                document.location.reload();
            }
            else {
                x = canvas.width/2;
                y = canvas.height-30;
                dx = 3;
                dy = -3;
                paddleX = (canvas.width-paddleWidth)/2;
            }
        }
    }
    
    if(rightPressed && paddleX < canvas.width-paddleWidth) {
        paddleX += 7;
    }
    else if(leftPressed && paddleX > 0) {
        paddleX -= 7;
    }
    
    x += dx;
    y += dy;
    requestAnimationFrame(draw);
}

   
    
    
