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
                    start: '2019-01-01',
                    end: '2019-01-05',
                    progress: 10,
                },
                {
                    id: '2',
                    name: 'World',
                    start: '2019-01-05',
                    end: '2019-01-10',
                    progress: 20,
                    dependencies: '1'
                },
                {
                    id: '3',
                    name: 'From',
                    start: '2019-01-10',
                    end: '2019-01-15',
                    progress: 30,
                    dependencies: '2'
                },
                {
                    id: '4',
                    name: 'Lloyd',
                    start: '2019-01-15',
                    end: '2019-01-20',
                    progress: 40,
                    dependencies: '3'
                }
            ],
            gantt: {},
            id:5,
        };
    },
    watch: {
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
        addnew(){
           var id =  this.id++;
      this.tasks.push({
                id: '' + id +'',
                name: 'lol ss' + this.id,
                start: '2019-01-01',
                end: '2019-01-05',
                progress: Math.random() * 100,
                 dependencies: '3'
            });             this.gantt.refresh(this.tasks);
        },
        changeVIewMode(mode){
              this.gantt.change_view_mode(mode);
        },
        setupGanttChart () {
            this.gantt = new Gantt(this.$refs.gantt, this.tasks, {
                on_click: task => {
                    console.log(task)
                    this.$emit('task-updated', task);
                },
                on_date_change: (task, start, end) => {
                                        console.log(task)

                    this.$emit('task-date-updated', { task, start, end });
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