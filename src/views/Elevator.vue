<template>
  <div>
    <h1>Elevator simulator</h1>
    <div class="simulator-info">
        <h2>Elevator information</h2>
        <p> Floorqueue index upward {{floorsInQueueUpwards}}</p>
        <p> Floorqueue index downward {{floorsInQueueDownwards}}</p>
        <p> current floor {{currentFloor}}</p>
        <p> going  {{ isGoingUp ?  'DOWN' : 'UP'}}</p>
    </div>


    <div class="building-wrapper">
        <!-- floors -->
        <div class="building-wrapper__floors">
            <div class="building-wrapper__floors-floor"  
                @click="requestFloor(floor.floorIndex)"
                v-for="(floor, i) in floors" 
                :key="i" 
                :class="{'active-floor': currentFloor === floor.floorIndex }">
                <p>{{floor.floorNumber}}</p>
                <img src="../../public/elevator.png" alt="elevator-door" >
            </div>
        </div>


        <!-- The elevator -->
        <div class="building-wrapper__elevator">
            <div class="building-wrapper__elevator-main-elevator" :style="{marginTop: currentFloor * 150 + 'px'}">
                <img src="../../public/elevatorroom.jpg" alt="elevator-room" >
                <div class="building-wrapper__elevator-main-elevator-floorpicker" v-if="isShowingFloorSelector">
                    <button 
                        @click="requestFloor(floor.floorIndex, i)"
                        v-for="(floor, i) in floors" 
                        :key="'select-floor' + i">
                        {{i}}
                    </button>
                </div>
            </div>
        </div>


    </div>
        


  </div>
</template>

<script lang="ts">
import { Component, Vue, Watch } from 'vue-property-decorator';

interface IFloor{
    floorIndex:number,
    floorNumber:number
}


@Component({
  components: {},
})

export default class Elevator extends Vue {
    floorsInQueueUpwards:Array<number> = [];
    floorsInQueueDownwards:Array<number> = [];
    isGoingUp:boolean = true;
    currentFloor:number = 3;  
    isShowingFloorSelector:boolean = false;
    floors:Array<IFloor> = [
        {floorIndex:0, floorNumber: 3},
        {floorIndex:1, floorNumber: 2},
        {floorIndex:2, floorNumber: 1},
        {floorIndex:3, floorNumber: 0},
    ];


    requestFloor(floor:number):void{
        this.isShowingFloorSelector = true;

        if(floor < this.currentFloor){
            this.floorsInQueueDownwards.push(floor);
        }else{
            this.floorsInQueueUpwards.push(floor);
        }

        this.removeDuplicatedFloorRequestsAndSortFloorOrder();
    }


    removeDuplicatedFloorRequestsAndSortFloorOrder():void{
        let uniqDownwards = [... new Set(this.floorsInQueueDownwards)];
        let uniqUpwards = [... new Set(this.floorsInQueueUpwards)];
        this.floorsInQueueDownwards = uniqDownwards;
        this.floorsInQueueUpwards = uniqUpwards;
        this.floorsInQueueUpwards.sort(function(a, b){return a-b});
        this.floorsInQueueDownwards.sort(function(a, b){return b-a});
    }




    triggerElevator():void{
        
        setInterval(() => { 

            //GO UPWARDS
            if(this.isGoingUp){
                //Set elevator to go down if no more upward requests
            if(this.currentFloor === 3){
                this.isGoingUp = false;
            }

            //Switch direction if no floors in queue
            if(this.isGoingUp && this.floorsInQueueUpwards.length === 0){
                this.isGoingUp = false;
            }

            //Set currentFloor and remove old one from queue
            if(this.floorsInQueueUpwards.length > 0){
                this.currentFloor = this.floorsInQueueUpwards[0];
                this.floorsInQueueUpwards.shift();
            }
        }



        //GO DOWNWARDS              
        if(!this.isGoingUp){
            
            //Set elevator to go up if no more downWards requests
            if(this.currentFloor === 0){
                this.isGoingUp = true;
            }

                //Switch direction if no floors in queue
            if(!this.isGoingUp && this.floorsInQueueDownwards.length === 0){
                this.isGoingUp = true;
            }

            //Set currentFloor and remove old one from queue
            if(this.floorsInQueueDownwards.length > 0){
                this.currentFloor = this.floorsInQueueDownwards[0];
                this.floorsInQueueDownwards.shift();
            }
        }

        },4000); //End of interval
        }




    //Lifecycles
    mounted():void{
        this.triggerElevator();
    }

    





}
</script>

<style  lang="scss">

.active-floor{
    font-weight: bold;
    color: #2f43ff;
    font-size: 1.2em;
    transition: 0.3s;
}

.simulator-info{
    width:50%;
    height: auto;
    margin: 10px auto;
    text-align: center;
    padding:5px;
}


button{
    display: inline-block;
    margin:0;
    text-align: center;
}

.building-wrapper{
    width:240px;
    height:auto;
    margin:0 auto;
    background:#4bd3f7;
    display: flex;


    &__buttons{
        width:120px;
        height:100%;
        display: inline-block;
        &-wrapper{
            width:120px;
            height:150px;
            button{
                margin-top: 70px;
                border: none;
                background:none;
                padding: 2px 6px;
            }
        }
    }


    &__floors{
        width:120px;
        height:100%;
        display: inline-block;
        &-floor{
            height:150px;
            background:#4bd3f7;
            border:1px solid #3dc2e5;
            box-sizing:border-box;
            text-align: center;

        img{
            width:60px;
            height: auto;
            display: block;
            margin: 0 auto;
            }
            &:hover{
                cursor:pointer;
            }
             &:hover::after{
                content: "Pick me up";
                position: absolute;
                margin-top: -48px;
                margin-left: -213px;
                background: #4bd3f7;
                border-radius: 10px;
                transition: .3s;
                font-size: 17px;
                padding: 0 18px;
                border-radius: 4px;
                color: #333;
                padding:10px 20px;
            }
        }
       
    }


    &__elevator{
        width:120px;
        height: 600px;;
        display: inline-block;
        &-main-elevator{
            width:120px;
            height:150px;
            transition: 0.3s;
            transition: 1s ease-out;
            img{
                width:50px;
                height: auto;
                display: block;
                margin: 0 auto;
                padding-top: 20px;
            }
            &-floorpicker{
                width: 90%;
                height: 69px;
                border-radius: 4px;
                display: block;
                margin: 0 5%;
                padding-top: 10px;
                button{
                    margin:1px;
                    border-radius: 50%;
                    cursor: pointer;
                }  
            }
        }
    }

}

</style>
