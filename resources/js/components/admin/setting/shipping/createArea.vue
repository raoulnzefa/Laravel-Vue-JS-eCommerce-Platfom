<template>
<div id="modal-form" class="modal fade" aria-hidden="true">
	<div class="modal-dialog">
		<div class="modal-content">
			<div class="modal-header text-right">
				<button class="btn btn-default" data-dismiss="modal">X</button>
			</div>
			<div class="modal-body">
				<div class="row">
					<div class="col-sm-12"><h3 class="m-t-none m-b">Add City</h3>
						<form @submit.prevent="save()" role="form">
							<div class="form-group">
								<label>City Name *</label> 
								<input v-model="area.name" type="text" placeholder="Area Name" class="form-control">
							</div>									
							<div class="form-group">
							<label>Status *</label>
                              <div class="switch">
                                <div class="onoffswitch">
                                    <input type="checkbox" v-model="area.status" checked="true" class="onoffswitch-checkbox" id="cityname">
                                    <label class="onoffswitch-label" for="cityname">
                                        <span class="onoffswitch-inner"></span>
                                        <span class="onoffswitch-switch"></span>
                                    </label>
                                </div>
                            </div>      
							</div>
							<div style="margin-bottom: 20px;">
								<button  class="btn btn-lg  btn-primary float-right " type="submit"><strong>{{ button_name }}</strong></button>
							</div>
						</form>
					</div>
				
					<div class="col-md-12" v-if="validation_error" style="margin-top: 20px">
						<div class="form-group">
							<div >
								<ul>
									<li class="text-danger" v-for="error in validation_error">{{ error[0] }}</li>
								</ul>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</div>
</div>

</template>


<script>
	
	import {EventBus} from  '../../../../vue-assets';

	import Mixin from  '../../../../mixin';

	export default {

		mixins : [Mixin],

		data(){

			return {

				area : {
					'name' : '',   
					'status' : true
				},

				button_name : "Save",
				validation_error : null, 
			}
		},


		methods : {
			save(){

             this.button_name = "Saving...";    
             axios.post(base_url+'admin/area/store',this.area)
                .then(response => {

                    if(response.data.status === 'success'){


                    $('#modal-form').modal('hide');

                    this.resetForm();
                    this.successMessage(response.data);
                    EventBus.$emit('order-area');

                    this.button_name = "Save";


					}

				   else
				    {
					  this.successMessage(response.data);	
					  this.button_name = "Save";
					}						
					

                    
                })
                .catch(err => {

                 if (err.response.status == 422) {

                    this.validation_error = err.response.data.errors;

                    this.validationError();

                    this.button_name = "Save";
                } 
                else 
                {

                    this.successMessage(err);

                    this.isloading = false;

                    this.button_name = "Save";
                }
             })

         },

         resetForm(){
          
          this.area = {

					'name' : '',   
					'status' : 1
				}

		  this.validation_error = null;		

         }



     }

 }

</script>