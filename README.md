import flash.display.Sprite;
import flash.utils.Timer;
import flash.events.TimerEvent;
import flash.events.KeyboardEvent;
import flash.ui.Keyboard;

//DECLARACION DE VARIABLES
const TS:uint=24;//Tamaño en pixeles de los azulejos que representan el campo de juego
var fieldArray:Array;//Matriz que representara al campo de juego
var fieldSprite:Sprite;//Representara el campo de juego
var figuras:Array = new Array();//Vector que guarda las figuras 
var colores:Array = new Array();
var figura:Sprite;
var ActualFigura:uint;
var SiguienteFigura:uint;
var ActualRotacion:uint;
var Horizontales:int;
var Vertical:int;
var Lado:int;
var timeCount:Timer=new Timer(500);
var gameOver:Boolean;

//LLAMADAS DE LAS FUNCION QUE TRABAJAMOS
generateField();//Funcion que trabaja como fondo
initFiguras();//Funcion que inicializara las figuras relacionadas con las matrices
generarFigura();//Genera una figura al azar para ser colocada en el campo de juego
stage.addEventListener(KeyboardEvent.KEY_DOWN, onKDown);

//FUNCIONES
function generateField():void
{
	fieldArray=new Array;
	fieldSprite=new Sprite;
	addChild(fieldSprite);
	fieldSprite.graphics.lineStyle(0/*Grosor de la linea*/,0x003399);//Aqui modificamos el color de la cuadricula
	for (var i:uint=0; i<20; i++) {
		fieldArray[i]=new Array  ;
		for (var j:uint=0; j<10; j++) {
			fieldArray[i][j]=0;
			fieldSprite.graphics.beginFill(0x003399);//Aqui se modifica el color de los cuadritos
			fieldSprite.graphics.drawRect(TS*j,TS*i,TS,TS);
			fieldSprite.graphics.endFill();
		}
	}
}
function initFiguras():void {
	// I Color Cian
	figuras[0]=[[[0,0,0,0],[1,1,1,1],[0,0,0,0],[0,0,0,0]],[[0,1,0,0],[0,1,0,0],[0,1,0,0],[0,1,0,0]]];
	colores[0]=0x00FFFF;
	// T Color Morado
	figuras[1]=[[[0,0,0,0],[1,1,1,0],[0,1,0,0],[0,0,0,0]],[[0,1,0,0],[1,1,0,0],[0,1,0,0],[0,0,0,0]],[[0,1,0,0],[1,1,1,0],[0,0,0,0],[0,0,0,0]],[[0,1,0,0],[0,1,1,0],[0,1,0,0],[0,0,0,0]]];
	colores[1]=0x767676;
	// L Color Naranja
	figuras[2]=[[[0,0,0,0],[1,1,1,0],[1,0,0,0],[0,0,0,0]],[[1,1,0,0],[0,1,0,0],[0,1,0,0],[0,0,0,0]],[[0,0,1,0],[1,1,1,0],[0,0,0,0],[0,0,0,0]],[[0,1,0,0],[0,1,0,0],[0,1,1,0],[0,0,0,0]]];
	colores[2]=0xFFA500;
	// J Color Azul
	figuras[3]=[[[1,0,0,0],[1,1,1,0],[0,0,0,0],[0,0,0,0]],[[0,1,1,0],[0,1,0,0],[0,1,0,0],[0,0,0,0]],[[0,0,0,0],[1,1,1,0],[0,0,1,0],[0,0,0,0]],[[0,1,0,0],[0,1,0,0],[1,1,0,0],[0,0,0,0]]];
	colores[3]=0x0000FF;
	// Z Color Rojo
	figuras[4]=[[[0,0,0,0],[1,1,0,0],[0,1,1,0],[0,0,0,0]],[[0,0,1,0],[0,1,1,0],[0,1,0,0],[0,0,0,0]]];
	colores[4]=0xFF0000;
	// S Color Verde
	figuras[5]=[[[0,0,0,0],[0,1,1,0],[1,1,0,0],[0,0,0,0]],[[0,1,0,0],[0,1,1,0],[0,0,1,0],[0,0,0,0]]];
	colores[5]=0x00FF00;
	// O Color Amarillo
	figuras[6]=[[[0,1,1,0],[0,1,1,0],[0,0,0,0],[0,0,0,0]]];
	colores[6]=0xFFFF00;
}
function generarFigura():void{
	if (! gameOver) {
		ActualFigura=Math.floor(Math.random()*7);
		ActualRotacion=0;
		Horizontales=0;
		if (figuras[ActualFigura][0][0].indexOf(1)==-1) {
			Horizontales=-1;
		}
		Vertical=3;
		dibujarFigura();
		if (canFit(Horizontales,Vertical,ActualRotacion)) {
			timeCount.addEventListener(TimerEvent.TIMER,onTime);
			timeCount.start();
		} else {
			gameOver=true;
		}
	}
}
function dibujarFigura():void {
	var ct:uint=ActualFigura;//Variable que guarda la figura actual
	figura=new Sprite ;
	addChild(figura);
	figura.graphics.lineStyle(0,0x000000);
	//Bucle principal
	for (var i:int=0; i<figuras[ct][ActualRotacion].length; i++) {
		for (var j:int=0; j<figuras[ct][ActualRotacion][i].length; j++) {
			if (figuras[ct][ActualRotacion][i][j]==1) {
				figura.graphics.beginFill(colores[ct]);
				figura.graphics.drawRect(TS*j,TS*i,TS,TS);
				figura.graphics.endFill();
			}
		}
	}
	colocarFigura();
}
function colocarFigura():void{
	figura.y=Horizontales*TS;
	figura.x=Vertical*TS;
}
function onKDown(e:KeyboardEvent):void{
	if(! gameOver){
	switch(e.keyCode){
		case 37 :
			if (canFit(Horizontales,Vertical-1,Lado)) {
				Vertical--;
				colocarFigura();
			}
			break;
		case 38 :
			var ct:uint=ActualRotacion;
			var rot:uint=(ct+1)%figuras[ActualFigura].length;
			if (canFit(Horizontales,Vertical,rot)) {
				ActualRotacion=rot;
				removeChild(figura);
				dibujarFigura();
				colocarFigura();
			}
			break;
		case 39 :
			if (canFit(Horizontales,Vertical+1,Lado)) {
				Vertical++;
				colocarFigura();
			}
			break;
		case 40 :
			if (canFit(Horizontales+1,Vertical,Lado)) {
				Horizontales++;
				colocarFigura();
			} else {
				Aterrizaje();//Gestiona el aterrizaje de la caida de la figura
				generarFigura();
			}
			break;
		}
	}
}
//Función, que quiere dos argumentos enteros de la fila y la columna de candidato, y devuelve verdadero si el tetromino actual encaja 
//en estas coordenadas, o falso si no encaja.
/*Una vez que encontramos una pieza tetromino en tetrominoes [ct] [currentRotation] [i] [j] , sabemos j es el valor de la columna dentro del tetromino y col es la columna de la candidata a la tetromino.

Si la suma de col y j es un número fuera de los límites del campo de juego, a continuación, al menos una parte de la tetromino está fuera del campo de juego, y la posición no es legal (retorno falso ) y no se hace nada.

Si todas las fichas del tetromino actual están dentro del campo de juego, entonces la posición es legal (devolver cierto ) y la posición de la tetromino se actualiza.
*/
function canFit(Ver:int,Hor:int,Lado:uint):Boolean{
	var ct:uint=ActualFigura;
	//Bucle
	for (var i:int=0; i<figuras[ct][ActualRotacion].length; i++) {
		for (var j:int=0; j<figuras[ct][ActualRotacion][i].length; j++) {
			if (figuras[ct][ActualRotacion][i][j]==1) {
				// out of left boundary
				if (Hor+j<0) {
					return false;//Si topa con el escenario solo llega hasta ahi
				}
				// out of right boundary
				if (Hor+j>9) {
					return false;
				}
				if (Ver+i>19){
					return false;
				}
				if (fieldArray[Ver+i][Hor+j]==1) {
					return false;
				}
			}
		}
	}
	return true;
}
function Aterrizaje():void{
	var ct:uint=ActualFigura;
	var Aterrizando:Sprite;//Se utiliza para cada pieza de la figura
	for (var i:int=0; i<figuras[ct][ActualRotacion].length; i++) {
		for (var j:int=0; j<figuras[ct][ActualRotacion][i].length; j++) {
			if (figuras[ct][ActualRotacion][i][j]==1) {
				Aterrizando=new Sprite;
				addChild(Aterrizando);//Muestra el sprite
				Aterrizando.graphics.lineStyle(0,0x000000);
				Aterrizando.graphics.beginFill(colores[ActualFigura]);
				Aterrizando.graphics.drawRect(TS*(Vertical+j),TS*(Horizontales+i),TS,TS);
				Aterrizando.graphics.endFill();
				Aterrizando.name="r"+(Horizontales+i)+"c"+(Vertical+j);
				fieldArray[Horizontales+i][Vertical+j]=1;
			}
		}	
	}
	removeChild(figura);
	//El timer se tiene que detener cuando la figura se detenga
	timeCount.removeEventListener(TimerEvent.TIMER, onTime);
	timeCount.stop();
	RevisarLineas();
}
//Funcion que valida cuando se llena una linea
function RevisarLineas():void{
	for (var i:int=0; i<20; i++) {
		if (fieldArray[i].indexOf(0)==-1) {
			for (var j:int=0; j<10; j++) {
				fieldArray[i][j]=0;
				removeChild(getChildByName("r"+i+"c"+j));
			}
			for (j=i; j>=0; j--) {
				for (var k:int=0; k<10; k++) {
					if (fieldArray[j][k]==1) {
						fieldArray[j][k]=0;
						fieldArray[j+1][k]=1;
						getChildByName("r"+j+"c"+k).y+=TS;
						getChildByName("r"+j+"c"+k).name="r"+(j+1)+"c"+k;
					}
				}
			}
		}
	}
}
//Funcion que mide el tiempo
function onTime(e:TimerEvent):void{
	if (canFit(Horizontales+1,Vertical,ActualRotacion)) {
		Horizontales++;
		colocarFigura();
	} else {
		Aterrizaje();
		generarFigura();
	}
}
