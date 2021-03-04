<template>
    <div>
        <svg ref="gantt" />
        <div>
            <button @click="changeVIewMode('Quarter Day')">Quarter Day</button>
             <button @click="changeVIewMode('Half Day')">Half Day</button>
              <button @click="changeVIewMode('Day')">Day</button>
               <button @click="changeVIewMode('Week')">Week</button>
               <button @click="changeVIewMode('Month')">Month</button>
        </div>
        <hr>
        <div>
         
               <button @click="addnew">Add</button>
              <button @click="test">test</button>

        </div>
    </div>
</template>

<script>
import Gantt from 'frappe-gantt';
export default {
  
   
    data () {
        return {
               mode: 'week',
            tasks: [
                {
                    id: '1',
                    name: 'Hello',
                    start: '2021-01-01',
                    end: '2021-01-05',
                    progress: 10,
                },  
                {
                    id: '2',
                    name: 'World',
                    start: '2021-01-05',
                    end: '2021-01-10',
                    progress: 20,
                    dependencies: '1'
                },
                {
                    id: '3',
                    name: 'From',
                    start: '2021-01-10',
                    end: '2021-01-15',
                    progress: 30,
                    dependencies: '2'
                },
                {
                    id: '4',
                    name: 'Morocco',
                    start: '2021-01-15',
                    end: '2021-01-20',
                    progress: 40,
                    dependencies: '3'
                }
            ],
            gantt: {},
            id:5,
            index:-1,
            isEnd:false,
        };
    },
    watch: {
        index(){
           var vm = this

   vm.index = -1
    vm.isEnd = false    
        },
        viewMode () {
            this.updateViewMode();
        },
        tasks () {
            this.updateTasks();
        }
    },
    mounted () {
        this.setupGanttChart();
    },
    methods: {
        test(){
            this.tasks[0].end = '2021-02-20'
        },
        formatDate(date) {
    var d = new Date(date),
        month = '' + (d.getMonth() + 1),
        day = '' + d.getDate(),
        year = d.getFullYear();

    if (month.length < 2) 
        month = '0' + month;
    if (day.length < 2) 
        day = '0' + day;

    return [year, month, day].join('-');
},
        addDays(date,days){
            date = new Date(date);
            const copy = new Date(Number(date))
            copy.setDate(date.getDate() + days)
        //    console.log(this.formatDate(copy))
        return this.formatDate(copy);
        },
        calculateDif(newdate,olddate){
                var date1 = new Date(olddate); 
                var date2 = new Date(newdate); 
                var Difference_In_Time = date2.getTime() - date1.getTime(); 
                var Difference_In_Days = Difference_In_Time / (1000 * 3600 * 24); 
            return Difference_In_Days;
        },
        addnew(){
      var id =  this.id++;
      this.tasks.push({
                id: '' + id +'',
                name: 'RANDOM XD ' + this.id,
                start: '2021-01-01',
                end: '2021-01-05',
                progress: Math.random() * 100,
                 dependencies: '3'
            });             
        },
        changeVIewMode(mode){
              this.gantt.change_view_mode(mode);
        },
        setupGanttChart () 
        {
            this.gantt = new Gantt(this.$refs.gantt, this.tasks, {
                on_click: task => {
                    console.log(task)
                    console.log(this.tasks[task._index])
                    this.$emit('task-updated', task);
                    alert(task)
                },
                 on_date_change: (task, start, end) => {  if(this.index == -1){
                            this.index = task._index
                
                          if(this.formatDate(start) == this.tasks[this.index].start && this.formatDate(end) != this.tasks[this.index].end){
                           this.isEnd = true;
                           var diftime = this.calculateDif(this.formatDate(end),this.tasks[task._index].end)
                           var i = this.index;
                        for(i; i < this.tasks.length - 1 ; i++){
                                this.tasks[i + 1].start = this.addDays(this.tasks[i + 1].start,diftime)
                                this.tasks[i + 1].end = this.addDays(this.tasks[i + 1].end,diftime)
                                
                        }  
                          this.tasks[this.index].end = this.formatDate(end)
                          this.tasks[this.index].start = this.formatDate(start)
                      
                         this.updateTasks()
                    }
                    else if (this.formatDate(start) != this.tasks[this.index].start && this.formatDate(end) == this.tasks[this.index].end){
                          this.isEnd = true;
                           var diftime = this.calculateDif(this.formatDate(start),this.tasks[task._index].start)
                           var i = this.index;   
                        for(i ; i > 0 ; i--){
                                this.tasks[i - 1].start = this.addDays(this.tasks[i - 1].start,diftime)
                                this.tasks[i - 1].end = this.addDays(this.tasks[i - 1].end,diftime)
                             
                                
                        }  
                          this.tasks[this.index].end = this.formatDate(end)
                          this.tasks[this.index].start = this.formatDate(start)
                      
                         this.updateTasks()
                     }
               }

                     if(!this.isEnd){
                        
                          console.log(task._index +  " _ " + this.formatDate(start))
                          console.log(task._index +  " _ " + this.formatDate(end))
                        this.tasks[task._index].end = this.formatDate(end)
                        this.tasks[task._index].start = this.formatDate(start)
                        //  this.updateTasks();
                     }
                    


                    setTimeout(() => {
                           
                            this.updateTasks();
                    }, 1000);
             
                },
                on_progress_change: (task, progress) => {
                                        console.log(task)

                    this.$emit('task-progress-updated', { task, progress });
                },
                on_view_change: (mode) => {
                    this.$emit('view-mode-updated', mode);
                }
            });
            this.updateTasks();
            this.updateViewMode();
        },
        updateViewMode () {
            this.gantt.change_view_mode('Week');
        },
        updateTasks () {
            this.gantt.refresh(this.tasks);
        }
    }
};
</script>

<style lang="scss">
@import "~frappe-gantt/dist/frappe-gantt.css";
</style>


//task1 1 -> 4 -> 5 
//task2  4 -> 7