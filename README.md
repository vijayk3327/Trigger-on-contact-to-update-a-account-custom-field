<a href="https://www.w3web.net/trigger-on-contact-to-update-a-account-custom-field/">Go to more details about this article__  <b><i>www.w3web.net</i></b></a><br/><br/>

  trigger contactTriggerRollSummary on Contact (before insert, after insert, before update, after update) {
    
    List<Contact> contactList= new List<Contact>();
    Set<Id> accountIdSet = new Set<Id>(); 
    
   if(trigger.isBefore){         
        system.debug('trigger before event');
        if(trigger.isUpdate){ 
            contactList=trigger.new;            
        }
        
        
    }else if(trigger.isAfter){        
        system.debug('trigger after event');   
        if(trigger.isDelete){
            contactList= trigger.old;
            system.debug('contactListOld ' + contactList);
        }else{
            contactList=trigger.new; 
            system.debug('contactListNew ' + contactList); 
        }          
        
        for(Contact c1:contactList){
            if(c1.AccountId !=null){
                accountIdSet.add(c1.AccountId);
            } 
            if(trigger.isUpdate){
                Contact oldContact = (Contact)trigger.oldMap.get(c1.Id);                 
                if(oldContact.AccountId != c1.AccountId){
                    accountIdSet.add(oldContact.AccountId);
                }                                
                
                List<Account> accObjList = new List<Account>();
                   for(Account parentSetObj: [Select Id, Name, contactSummary__c, (Select Id, FirstName, LastName From Contacts) From Account Where Id IN:accountIdSet])
                   {
                       List<Contact> childObj = parentSetObj.Contacts;
                       String conStr = '';
                       for(Contact con:childObj){
                           conStr = conStr + con.FirstName + ' ' + con.LastName + '\n';
                       }
                        parentSetObj.contactSummary__c = conStr;
                        accObjList.add(parentSetObj);
                    }
                    update accObjList;               
                
            }             
        }
  }
}
