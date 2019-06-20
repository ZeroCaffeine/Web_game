<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8"/>
	<title>웹게임</title>
	<style>	
		* { padding : 0; margin : 0; }
		canvas { background : #eee; display : block; margin : 0 auto;}
	</style>
</head>
<body>
	<canvas id="canvas" width="480" height="320"></canvas>
	<script>
		var canvas = document.getElementById("canvas");
		var context = canvas.getContext("2d");
	
		var x = canvas.width / 2;
		var y = canvas.height - 30;
		var dx = 3;
		var dy = -3;
		var ballRadius = 10;
		var paddleWidth = 75;
		var paddleHeight = 10;
		var paddleX = (canvas.width - paddleWidth) / 2;
		var paddleSpeed = 5;
		var rightPressed = false;
		var leftPressed = false;
		document.addEventListener("keydown", keyDownHandler, false);
		document.addEventListener("keyup", keyUpHandler, false);
		document.addEventListener("mousemove", mouseMoveHandler, false);
		// 행렬 개수
		var brickRowCount = 3;
		var brickColumnCount = 5;
		// 벽돌 가로/세로
		var brickWidth = 75;
		var brickHeight = 20;
		// 벽돌 (좌우) 간격
		var brickPadding = 10;
		// 벽돌 화면 간격
		var brickOffsetTop = 30;
		var brickOffsetLeft = 30;
		var bricks = [];
		for(var r = 0; r < brickRowCount; r++) {
			bricks[r] = [];
			for(var c = 0; c < brickColumnCount; c++) {
				bricks[r][c] = { x: 0, y: 0, status: 1 };
			}
		}
		var score = 0;
		var lives = 3;
		//var interval = setInterval(draw, 1000);
		function drawLives() {
			context.font = "16px Arial";
			context.fillStyle = "#0095DD";
			context.fillText("목숨 : "+lives, canvas.width-65, 20);
		}
		function drawScore() {
			context.font = "16px Arial";
			context.fillStyle = "#0095DD";
			context.fillText("Score: "+score, 8, 20);
		}
		function collisionDetection() {
			for(var r = 0; r < brickRowCount; r++) {
				for(var c = 0; c < brickColumnCount; c++) {
					var brick = bricks[r][c];
				
					if (brick.status == 1 && 
						x > brick.x && x < brick.x + brickWidth && 
						y > brick.y && y < brick.y + brickHeight) {
						dy = -dy;
						brick.status = 0;
						score++;
						if(score == brickRowCount * brickColumnCount) {
							alert("게임 클리어!");
							document.location.reload();
							//clearInterval(interval);
						}
					}
				}
			}
		}
		function mouseMoveHandler(event) {
			var relativeX = event.clientX - canvas.offsetLeft;
			if(relativeX > 0 && relativeX < canvas.width) {
				paddleX = relativeX - paddleWidth / 2;
			}
		}
		function keyUpHandler(event) {
			if (event.keyCode == 39) {
				rightPressed = false;
			} else if (event.keyCode == 37) {
				leftPressed = false;
			}
		}
		function keyDownHandler(event) {
			if (event.keyCode == 39) {
				rightPressed = true;
			} else if (event.keyCode == 37) {
				leftPressed = true;
			}
		}
		function drawBall() {
			context.beginPath();
			context.arc(x, y, ballRadius, 0, Math.PI * 2);
			context.fillStyle = "#0095DD";
			context.fill();
			context.closePath();
		}
		function drawPaddle() {
			if (rightPressed && paddleX < canvas.width - paddleWidth) {
				paddleX += paddleSpeed;
			}
			if (leftPressed && paddleX > 0) {
				paddleX -= paddleSpeed;
			}
			context.beginPath();
			context.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
			context.fillStyle = "#0095DD";
			context.fill();
			context.closePath();
		}
		function drawBricks() {
			for(var r = 0; r < brickRowCount; r++) {
				for(var c = 0; c < brickColumnCount; c++) {
					if (bricks[r][c].status == 1) {
						var brickX = (c * (brickWidth + brickPadding)) + brickOffsetLeft;
						var brickY = (r * (brickHeight + brickPadding)) + brickOffsetTop;
					
						bricks[r][c].x = brickX;
						bricks[r][c].y = brickY;
					
						context.beginPath();
						context.rect(brickX, brickY, brickWidth, brickHeight);
						context.fillStyle = "#0095DD";
						context.fill();
						context.closePath();
					}
				}
			}
		}
		
		function draw() {
			context.clearRect(0, 0, canvas.width, canvas.height);
			drawBricks();
			drawBall();
			drawPaddle();
			collisionDetection();
			drawScore();
			drawLives();
			if (x + ballRadius / 2 + dx > canvas.width || x - ballRadius / 2 + dx < 0) {
				dx = -dx;
			}
			if (y - ballRadius / 2 + dy < 0) {
				dy = -dy;
			} else if (y + dy > canvas.height-ballRadius) {
				if(x > paddleX && x < paddleX + paddleWidth) {
					dy = -dy;
				}
				else {
					lives--;
					if(!lives) {
						alert("GAME OVER");
						document.location.reload();
						clearInterval(interval); // Needed for Chrome to end game
					}
					else {
						x = canvas.width/2;
						y = canvas.height-30;
						dx = 2;
						dy = -2;
						paddleX = (canvas.width-paddleWidth)/2;
					}
				}
			}
			x += dx;
			y += dy;
			requestAnimationFrame(draw);
		}
		// 10 밀리 초마다 실행
		draw();
	</script>
</body>
</html>
