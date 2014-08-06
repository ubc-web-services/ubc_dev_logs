
This module is to help you create custom logs while creating custom modules.

It allows you to customize your logs to any granularity, and allows you to dump different data types to logs. Dump strings, arrays, objects to a log line.

Logs lines are given machine generated timestamps, and are sorted by date.
{module name}/logs/y/m/d/your_custom_log_names.log


******************************************************************************
CONFIG SETUP:


1) In the .info file of your custom module, add ubc_dev_logs as a dependency:
		
	dependencies[] = ubc_dev_logs


2) Create a directory called 'logs' in your module folder


3) Change the user group of your module:

	cd {drupal site name}/modules
	chgrp -R apache {module name}


4) Change the permissions of the logs folder

	cd {drupal site name}/modules/{module name}
	chmod 775 logs



******************************************************************************
MODULE SETUP:

1) Define a constant for your module name:

	define('UBC_SAMPLE_MOD_NAME', 'ubc_sample_mod');


2) Implement {module name}_getLogVars() 
   Define log retention period, and the names of your logs:

function ubc_sample_mod_getLogVars() {

  $vars = array();

  // NUMBER OF DAYS BEFORE DELETING LOGS
  $vars['UBC_DEV_LOGS_LOG_DELETE_PERIOD'] = 6;

  // INSERT THE NAMES OF YOUR SAMPLE LOGS
  // START WITH INDEX 1
  $LOG_NAMES_ARRAY = array();
  $LOG_NAMES_ARRAY[1] = 'ubc_sample_mod.log';
  $LOG_NAMES_ARRAY[2] = 'ubc_sample_mod.web.log';
  $LOG_NAMES_ARRAY[3] = 'ubc_sample_mod.cron.log';
  $LOG_NAMES_ARRAY[4] = 'ubc_sample_mod.ex.log';
  $LOG_NAMES_ARRAY[5] = 'ubc_sample_mod.server.log';

  $vars['UBC_DEV_LOGS_FILES'] = serialize($LOG_NAMES_ARRAY);

  return $vars;
}



******************************************************************************
USAGE:

1) Log data from your module:

  $mod = UBC_SAMPLE_MOD_NAME;
  $data = string | array | object - the data to dump to the log
  $type = string - Name of Log Line (ex. name of a function, type of query)
  $num = int - index of file to log this line to, as defined in $LOG_NAMES_ARRAY 
	
  ubc_dev_logs_logTransaction($mod, $data, $type, $num);


2) Sample output:

  Machine timestamp   | $type         |	$data
  ------------------------------------------------------------
  2014-03-25::00:01:04	(SRS CONNECT)	START
  2014-03-25::00:01:04	(SRS CONNECT)	SUCCESS
  2014-03-25::00:01:04	(UPDATE RECORDS)	term
  2014-03-25::00:01:04	(UPDATE RECORDS QUERY)	SELECT * FROM AUD_TERM WHERE AUD_ID > 10050974 ORDER BY AUD_ID ASC
  2014-03-25::00:01:04	(UPDATE RECORDS COUNT)	0
  2014-03-25::00:01:04	<SRS QUERY>	SELECT MAX(AUD_ID) AS MAX FROM AUD_TERM
  2014-03-25::00:01:04	<SRS QUERY RESULT>	10050974
  2014-03-25::00:01:04	(TABLE SYNC)	9	course	COURSE	WEB_FLAG	Y	4149	10798727
	

3) Viewing logs: You can open the entire log file(s), or to view from the server as they are generated:

	cd {drupal site name}/modules/{module name}/logs/y/m/d
	tail -f {logname}


	Pipe the log $type to the output:

	tail -f {logname} | $type

