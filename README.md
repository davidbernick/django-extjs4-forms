django-extjs4-forms
===================

Use your regular django forms (forms.py) to create extjs4 forms in your formpanels on the fly.

This is taken, in large part, from [https://github.com/revolunet/django-extjs]

Setup
======
Stuff in the Static folder should go into an area that can be read by your html/javascript. (ie wherever you usually put your javascript/css/images).

The stuff in the src directory can be manually manipulated or you can use setup.py (usual commands).

	# the django forms.py
	
	from django_extjs4_forms.forms import ExtJsForm

	# the form definition (could also be a ModelForm)
    class ContactForm(forms.Form):
        name = forms.CharField(label='your name')
        phone = forms.CharField(label='phone number', required = False)
        mobile_type = forms.CharField(label='phone type', required = True)
        mobile_type.choices = [
             ('ANDROID','Android')
            ,('IPHONE','iPhone')
            ,('SYMBIAN','Symbian (nokia)')
            ,('OTHERS','Others')
        ]
        email = forms.EmailField(label='your email', initial='test@revolunet.com')
        message = forms.CharField(label='your message', widget = forms.widgets.Textarea(attrs={'cols':15, 'rows':5}))

    ExtJsForm.addto(ContactForm)        # new methods added to the form
            
    # the views.py
    def contact_form(request, path = None):
        if request.method == 'POST':
            # handle form submission
            form = ContactForm(request.POST)
            if not form.is_valid():
                return utils.JsonError(form.html_errorlist())
            else:
                # send your email
                print 'send a mail'
            return utils.JsonResponse(utils.JSONserialise({
                'success':True, 
                'messages': [{'message':'OK'}]
                }) )
        else:
            # handle form display
            form = ContactForm()
            return utils.JsonResponse(utils.JSONserialise(form.as_extjsfields()))
            

    # the javascript (ExtJs 4) :
	new Ext.ux.django.DjangoForm({
			            border:false
			            ,intro:'Pick a role'
			            ,showButtons:true
			            ,showSuccessMessage:'Form submission success'
			            ,url:'/the/url/that/responds/with/forms/' 
			            ,scope:this
			            ,submitSuccess: function(){
			            	//stuff to put on success if this is a window or something like that
			            }
			             ,callback:function(form) {
			                form.doLayout();
			             }
			       })    
