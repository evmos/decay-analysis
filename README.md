### Description

The end goal was to calculate the amount that each sender of the triggered claim events in history had failed to receive due to a decay bug. In other words:

`how much should the user had received` - `how much did the user actually received`

To achieve this the following steps were followed:

1.  Collect on a database all the `claim` and `merge_claim_records` events that occurred in Evmos’s history using https://github.com/facs95/decay-data.
    - This process is explained in detail within the mentioned repo.
2. Data was cleaned and filtered using pandas to generate a table with the following columns:
    - `sender` - the address that received the claim rewards
    - `height` - the height at which the event occurred
    - `event_type` - action type of the event that got triggered
        - ACTION_EVM
        - ACTION_DELEGATE
        - ACTION_VOTE
        - ACTION_IBC_TRANSFER
    - `initial_claimable_amount` - claim amount available at genesis for the `sender` if any
    - `claimable_at_event` - the amount available to claim for the `sender` at the event if any.
        - If there were any `merge_claims_records`  events present for the `sender` account before the event `height`, then the claimable amount at genesis of both accounts were taking into account and added.
    - `amount_claimed` - the amount the address actually received on the event
    - `lost_amount` - the difference between `claimable_at_event` and `amount_claimed`
        - This is the amount that should be returned.
    
    Details about how this information was processed can be found under `./decay-data.ipynb`
    

### Dependencies

- Jupyter
- Pandas
- Numpy
- Sqlite3

### Results

The results are exposed under `claim_event_decay_loss.csv` table. 