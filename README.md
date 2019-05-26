# Pongnj
<!DOCTYPE html>

<html style = "background-color: black;">

    <head>
        <meta charset="UTF-8">
    </head>

    <body onload="initialisation()">
        <canvas id="canvas" style="border:1px solid #f1f1f1;"></canvas>
        <script>
            //definition des constantes du jeu
            const largeur = 750;
            const hauteur = 750;

            //Polygone
            const nbjoueur = 7;
            const cote = largeur*Math.sin(Math.PI / nbjoueur);
            const anglecentre = 2*Math.PI / nbjoueur
            const anglePol = Math.PI * (nbjoueur - 2) / nbjoueur
            let beta = [(Math.PI - anglePol) / 2]
            let ptpolx = [largeur/2]
            let ptpoly = [0]
            for (i=1; i<=nbjoueur;i++){
                ptpolx.push((largeur/2)*(1+Math.sin(anglecentre*i))),
                ptpoly.push((largeur/2)*(1-Math.cos(anglecentre*i))),
                beta.push(Math.PI - anglePol + beta[i-1])
            }
            let a = 0
            let b = 0
            let dtpol = []
            let proportion = 0
            let antirebond = false

            //Score
			const largeurScore = 150;
			const hauteurScore = 30*nbjoueur + 60
			const scoreInitial = 2

            // palettes
            const couleurPalettes = ["blue","green","red","yellow","darkcyan","orange","purple","skyblue","pink","mangenta","darkblue","lime","deepskyblue","darkgrey","cyan","navajowhite","rosybrown","darkkhaki","slategrey","chocolate","crimson"]
            for (i=0; i<=21;i++){
                couleurPalettes.push(couleurPalettes[i]) }
			var deplacementPalette = []
			for(i=0; i<nbjoueur; i++){
				deplacementPalette.push(0) }
            let palettes = []
            for (i=0; i<nbjoueur;i++){
                palettes.push([2,3]) 
            }
            //definition des parametres du cercle inscrit
            const cercle = {
                Ox: (largeur / 2),
                Oy: (hauteur / 2),
                rayon: largeur / 2,
                couleur: "#252525"
            }
             //definition des parametres de la balle
            const rayonBalle = 5;
            let angleballe = 2*Math.PI * Math.random()
            let vballe = 0
            var balle = {
                x: (largeur / 2),
                y: (hauteur / 2),
                angle: angleballe,
                v: 2,
                vx: vballe * Math.cos(angleballe),
                vy: vballe * Math.sin(angleballe),
                couleur: "red"
            }

                function initialisation() {
                    setInterval(affichage, 10);
                    //creation du canvas 
                    canvas.width = largeur;
                    canvas.height = hauteur;
                    canvas.style = "position: absolute; top: 0px; left: 0px; right: 0px; bottom: 0px; margin: auto; border:2px solid";
                
                    //affichage du polygone
                    var ctx = canvas.getContext('2d');
                    
                    // affichage cercle
                    ctx.beginPath();
                    ctx.arc(cercle.Ox, cercle.Oy, cercle.rayon, Math.PI*2, false);
                    ctx.closePath();
                    ctx.fillStyle = cercle.couleur;
                    ctx.fill();
                        
                    //dtpol
                    for (i=0; i<nbjoueur;i++){
                        a = (ptpoly[i+1]-ptpoly[i])/(ptpolx[i+1]-ptpolx[i])
                        b = ptpoly[i] - a * ptpolx[i]
                        dtpol.push([a,b])
                    }

                    // palettes
                    for (i=0; i<nbjoueur;i++){
                        ctx.beginPath();
                        ctx.moveTo(ptpolx[i] + 2*cote*Math.cos(beta[i])/5, ptpoly[i] + 2*cote*Math.sin(beta[i])/5);
                        ctx.lineTo(ptpolx[i] + 3*cote*Math.cos(beta[i])/5, ptpoly[i] + 3*cote*Math.sin(beta[i])/5);
                        ctx.strokeStyle = couleurPalettes[i]
                        ctx.stroke()
                        ctx.closePath();
                    }
                    //creation de la liste des score
                    var scores = []
                    for(i=0; i<nbjoueur; i++){
                        scores.push(scoreInitial)
                    }
                                    
                }

                function rebond(){
                    if ((ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] + cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] + cote*Math.cos(beta[i])/5 )){
                        balle.angle = 2 * beta[i] - balle.angle
                        //balle.x = balle.x + 2 * vballe * Math.cos(balle.angle)
                        //balle.y = balle.y + 2 * vballe * Math.sin(balle.angle)
                        console.log("rebond bord  "+i)
                    }
                    if ((ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] - cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] - cote*Math.cos(beta[i])/5 )){
                        balle.angle = 2 * beta[i] - balle.angle
                        //balle.x = balle.x + 2 * vballe * Math.cos(balle.angle)
                        //balle.y = balle.y + 2 * vballe * Math.sin(balle.angle)
                        console.log("rebond bord  "+i)
                    }
                    else if((ptpolx[i] + palettes[i][0]*cote*Math.cos(beta[i])/5 > balle.xsuiv && balle.xsuiv > ptpolx[i] + palettes[i][1]*cote*Math.cos(beta[i])/5)||(ptpolx[i] + palettes[i][0]*cote*Math.cos(beta[i])/5 < balle.xsuiv && balle.xsuiv < ptpolx[i] + palettes[i][1]*cote*Math.cos(beta[i])/5)){
                        proportion = (Math.abs(( palettes[i][1]*cote*Math.cos(beta[i])/5 - balle.x ) / (palettes[i][1]*cote*Math.cos(beta[i])/5 - palettes[i][0]*cote*Math.cos(beta[i])/5)) - .5 )*2
                        balle.angle = (balle.angle + Math.PI) + (proportion * (Math.PI/3))
                        //balle.x = balle.x + vballe * Math.cos(balle.angle)
                        //balle.y = balle.y + vballe * Math.sin(balle.angle)
                        console.log("rebond palette   "+i)
                    }
                    else {
                        balle.x = largeur/2
                        balle.y = largeur/2
                        balle.angle = 2*Math.PI * Math.random()
                        vballe = 0
                        console.log("perdu                 rebond")
                    }
                    antirebond = true
                    setTimeout(function (){ antirebond = false }, 30)
                }
                //rebond sur le polygone
                function rebondPol(){
                    if( antirebond == false){
                        for (i=0; i<nbjoueur;i++){
                                a = dtpol[i][0]
                                b = dtpol[i][1]
                            if (beta[i] >= Math.PI * 3 / 2 - 0.1 && beta[i] <= Math.PI * 3 / 2 + 0.1 ){
                                if( balle.xsuiv < ptpolx[i]){
                                    if (ptpolx[i] < balle.xsuiv < ptpolx[i] + cote*Math.cos(beta[i])/5 || ptpolx[i] > balle.xsuiv > ptpolx[i] + cote*Math.cos(beta[i])/5 || ptpolx[i] + palettes[i][0]*cote*Math.cos(beta[i])/5 < balle.xsuiv < ptpoly[i] + palettes[i][1]*cote*Math.sin(beta[i])/5 || ptpolx[i] + palettes[i][0]*cote*Math.cos(beta[i])/5 > balle.xsuiv > ptpoly[i] + palettes[i][1]*cote*Math.sin(beta[i])/5 ){
                                        balle.angle =  2 * beta[i] - balle.angle
                                        console.log("gauche   "+i)
                                    }
                                }
                            }
                            else if (beta[i] >= Math.PI / 2 - 0.1 && beta[i] <= Math.PI / 2 + 0.1  && balle.xsuiv > ptpolx[i] ){
                                if ((ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] + cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] + cote*Math.cos(beta[i])/5 )||(ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] - cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] - cote*Math.cos(beta[i])/5 )){
                                    balle.angle = 2 * beta[i] - balle.angle
                                    console.log("droite   "+i)
                                }
                            }
                            else if (beta[i] >= Math.PI - 0.2 && beta[i] <= Math.PI + 0.2  && balle.ysuiv > ptpoly[i]){
                                if ((ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] + cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] + cote*Math.cos(beta[i])/5 )||(ptpolx[i] < balle.xsuiv && balle.xsuiv < ptpolx[i] - cote*Math.cos(beta[i])/5 )||( ptpolx[i] > balle.xsuiv && balle.xsuiv > ptpolx[i] - cote*Math.cos(beta[i])/5 )){
                                    balle.angle = 2 * beta[i] - balle.angle 
                                    console.log(i+"   bas")
                                }
                            }
                            else if ((i <= (Math.round(nbjoueur / 4) - 1) || nbjoueur - i <= Math.round(nbjoueur / 4))){
                                if (balle.ysuiv <= a * balle.xsuiv + b){
                                    rebond()
                                }
                            }
                            else if (balle.ysuiv >= a * balle.xsuiv + b){
                                rebond()
                            }
                        }
                    }
                }
                function affichage() {
                    var canvas = document.getElementById('canvas');
                    if (canvas.getContext) {
                        var ctx = canvas.getContext('2d');
                        //creation du canvas 
                        canvas.width = largeur;
                        canvas.height = hauteur;
                        canvas.style = "position: absolute; top: 0px; left: 0px; right: 0px; bottom: 0px; margin: auto; border:2px solid";

                        //affichage du polygone
                        var ctx = canvas.getContext('2d');
                            for (i=1; i<=nbjoueur;i++){
                                ctx.beginPath();
                                ctx.moveTo(ptpolx[i],ptpoly[i]);
                                ctx.lineTo(ptpolx[i] - cote*Math.cos(beta[i-1])/5, ptpoly[i] - cote*Math.sin(beta[i-1])/5);
                                ctx.moveTo(ptpolx[i],ptpoly[i]);
                                ctx.lineTo(ptpolx[i] + cote*Math.cos(beta[i])/5, ptpoly[i] + cote*Math.sin(beta[i])/5);
                                ctx.strokeStyle = "white"
                                ctx.stroke()
                                ctx.closePath();
                            }

                        // affichage de la balle
                        balle.xsuiv = balle.x + vballe * Math.cos(balle.angle)
                        balle.ysuiv = balle.y + vballe * Math.sin(balle.angle)
                        balle.x = balle.xsuiv
                        balle.y = balle.ysuiv
                        ctx.beginPath();
                        ctx.arc(balle.x, balle.y, rayonBalle, 0, Math.PI*2);
                        ctx.closePath();
                        ctx.fillStyle = balle.couleur;
                        ctx.fill();

					
                        //deplacement des palettes
                        for(i=0; i<nbjoueur; i++){
                            if(deplacementPalette[i] == 1 && (palettes[i][1] < 4)){
                                palettes[i][0] += 0.03
                                palettes[i][1] += 0.03
                            }
                            if(deplacementPalette[i] == -1 && (palettes[i][0] > 1)){
                                palettes[i][0] -= 0.03
                                palettes[i][1] -= 0.03
                            }
                        }

                        // palettes
                        for (i=0; i<nbjoueur;i++){
                            ctx.beginPath();
                            ctx.moveTo(ptpolx[i] + palettes[i][0]*cote*Math.cos(beta[i])/5, ptpoly[i] + palettes[i][0]*cote*Math.sin(beta[i])/5);
                            ctx.lineTo(ptpolx[i] + palettes[i][1]*cote*Math.cos(beta[i])/5, ptpoly[i] + palettes[i][1]*cote*Math.sin(beta[i])/5);
                            ctx.strokeStyle = couleurPalettes[i]
                            ctx.stroke()
                            ctx.closePath();
                        }
                        rebondPol()
                        if (((balle.xsuiv - largeur/2)**2 + (balle.ysuiv - largeur/2)**2)**.5 > largeur/2 + 20){
                            balle.x = largeur/2
                            balle.y = largeur/2
                            balle.angle = 2*Math.PI * Math.random()
                            vballe = 0
                            console.log("perdu   fin")
                        }



                    }
                }
                

            document.addEventListener('keydown', function(event) {
                //Pause
                if (event.keyCode == 32) {
                    console.log("Pause")
                    if (vballe != 0){
                        balle.v = vballe;
                        vballe = 0
                    } else {
                        vballe = balle.v
                    }

                }
            });

            //deplacement des palettes
			document.addEventListener('keydown', function(event) {
				for(i=0; i<nbjoueur; i++){
					if(event.keyCode == 37){
						deplacementPalette[i] = 1
					}
					if(event.keyCode == 39){
						deplacementPalette[i] = -1
					}
				}
			})
			document.addEventListener('keyup', function(event) {
				for(i=0; i<nbjoueur; i++){
					if(event.keyCode == 37){
						deplacementPalette[i] = 0
					}
					if(event.keyCode == 39){
						deplacementPalette[i] = 0
					}
				}
            });
        </script>
    </body>

</html>

