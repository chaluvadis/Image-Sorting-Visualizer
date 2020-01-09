<template id="image-capture">
    <div>
        <div v-show="!started">
            <div class="container h-100 d-flex justify-content-center">
                <div class="my-auto">
                    <h1 class="text-center">Sorting Image Visualizer</h1>
                    <hr>
                    Rows: <input type="text" v-model="rows">
                    Cols: <input type="text" v-model="cols">
                    <br><br>
                    <input type="text" style="width:100%" placeholder="Copy Image URL" v-model="link">
                    <br><br>
                    <button class="btn-success col text-center" @click="start">Start</button>
                </div>
            </div>
        </div>
        <div v-show="started">
            <div class="container d-flex justify-content-center">
                <div class="btn-toolbar pull-right">
                    <button class="btn btn-primary btn-space" @click="insertion">Insertion sort</button>
                    <button class="btn btn-primary btn-space" @click="selection">Selection sort</button>
                    <button class="btn btn-primary btn-space" @click="bubble">Bubble sort</button>
                </div>
            </div>
            <br>
            <div class="container h-100 d-flex justify-content-center">
                <canvas id="canvas" ref="canvas" width=300 height=300>
                </canvas>
            </div>
        </div>
    </div>
</template>

<script>
    import {selectionSort, bubbleSort, insertionSort} from "../sorts";

    export default {
        name: 'ImageVisualizer',
        data: function () {
            return {
                randoms: [],
                canvas: null,
                ctx: null,
                rows: 3,
                cols: 3,
                img: null,
                link: null,
                pieceWidth: null,
                pieceHeight: null,
                started: false,
                startIndex:0,
                sorted:false,
            }
        },
        methods: {
            insertion: function() {
                this.startIndex = 0;
                let self = this;

                setInterval(function(){
                   insertionSort(self)
                }, 1000);
            },
            bubble: function() {
                this.sorted = false;
                let self = this;

                setInterval(function() {
                    bubbleSort(self)
                }, 1000)
            },
            selection: function() {
                this.startIndex = 0;
                let self = this;

                setInterval(function(){
                    selectionSort(self)
                }, 1000);
            },
            start: function() {
                this.started = true;
                this.canvas=this.$refs.canvas;
                this.ctx =this.canvas.getContext("2d");
                let self = this;

                this.img=new Image();
                this.img.src=this.link;
                this.img.onload= function() {
                    self.canvas.width=self.img.width;
                    self.canvas.height=self.img.height;
                    self.pieceWidth=self.canvas.width/self.cols;
                    self.pieceHeight=self.canvas.height/self.rows;

                    for(let i = 0; i < self.rows; i++) {
                        for(let j = 0; j<self.cols; j++) {
                            let part = {col:j, row:i, val: (i*10)+j}
                            self.randoms.push(part)
                        }
                    }
                    for(let j, x, i = self.randoms.length; i; j = Math.floor(Math.random() * i), x = self.randoms[--i], self.randoms[i] = self.randoms[j], self.randoms[j] = x);

                    self.drawImage();
                };
            },
            highlight: function(first, second) {
                this.ctx.strokeStyle = "#cfe627";
                this.ctx.lineWidth = 10;
                this.ctx.strokeRect(this.randoms[first].col* this.pieceWidth,this.randoms[first].row * this.pieceHeight, this.pieceWidth, this.pieceHeight )
                this.ctx.strokeStyle = "#cfe627";
                this.ctx.lineWidth = 10;
                this.ctx.strokeRect(this.randoms[second].col * this.pieceWidth, this.randoms[second].row * this.pieceHeight, this.pieceWidth, this.pieceHeight)
                this.drawImage();
            },
            drawImage: function() {
                let self = this
                setTimeout(function(){
                    let i=0;

                    for(let y=0;y<self.rows;y++){
                        for(let x=0;x<self.cols;x++){
                            let p=self.randoms[i++];
                            self.ctx.drawImage(
                                self.img,
                                x*self.pieceWidth, y*self.pieceHeight, self.pieceWidth, self.pieceHeight,
                                p.col*self.pieceWidth, p.row*self.pieceHeight, self.pieceWidth, self.pieceHeight
                            );
                        }}
                }, 500)
            },
        }
    }
</script>

<style>
    body {
        background-color: ivory;
        padding: 10px;
    }

    .btn-space {
        margin-right: 5px;
    }
</style>