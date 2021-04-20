
line 1272 ble_gap.h:
typedef struct
{
  ble_gap_adv_report_type_t type;                  /**< Advertising report type. See @ref ble_gap_adv_report_type_t. */
  ble_gap_addr_t            peer_addr;             /**< Bluetooth address of the peer device. If the peer_addr is resolved:
                                                        @ref ble_gap_addr_t::addr_id_peer is set to 1 and the address is the
                                                        peer's identity address. */
  ble_gap_addr_t            direct_addr;           /**< Contains the target address of the advertising event if
                                                        @ref ble_gap_adv_report_type_t::directed is set to 1. If the
                                                        SoftDevice was able to resolve the address,
                                                        @ref ble_gap_addr_t::addr_id_peer is set to 1 and the direct_addr
                                                        contains the local identity address. If the target address of the
                                                        advertising event is @ref BLE_GAP_ADDR_TYPE_RANDOM_PRIVATE_RESOLVABLE,
                                                        and the SoftDevice was unable to resolve it, the application may try
                                                        to resolve this address to find out if the advertising event was
                                                        directed to us. */
  uint8_t                   primary_phy;           /**< Indicates the PHY on which the primary advertising packet was received.
                                                        See @ref BLE_GAP_PHYS. */
  uint8_t                   secondary_phy;         /**< Indicates the PHY on which the secondary advertising packet was received.
                                                        See @ref BLE_GAP_PHYS. This field is set to @ref BLE_GAP_PHY_NOT_SET if no packets
                                                        were received on a secondary advertising channel. */
  int8_t                    tx_power;              /**< TX Power reported by the advertiser in the last packet header received.
                                                        This field is set to @ref BLE_GAP_POWER_LEVEL_INVALID if the
                                                        last received packet did not contain the Tx Power field.
                                                        @note TX Power is only included in extended advertising packets. */
  int8_t                    rssi;                  /**< Received Signal Strength Indication in dBm of the last packet received.
                                                        @note ERRATA-153 and ERRATA-225 require the rssi sample to be compensated based on a temperature measurement. */
  uint8_t                   ch_index;              /**< Channel Index on which the last advertising packet is received (0-39). */
  uint8_t                   set_id;                /**< Set ID of the received advertising data. Set ID is not present
                                                        if set to @ref BLE_GAP_ADV_REPORT_SET_ID_NOT_AVAILABLE. */
  uint16_t                  data_id:12;            /**< The advertising data ID of the received advertising data. Data ID
                                                        is not present if @ref ble_gap_evt_adv_report_t::set_id is set to
                                                        @ref BLE_GAP_ADV_REPORT_SET_ID_NOT_AVAILABLE. */
  ble_data_t                data;                  /**< Received advertising or scan response data. If
                                                        @ref ble_gap_adv_report_type_t::status is not set to
                                                        @ref BLE_GAP_ADV_DATA_STATUS_INCOMPLETE_MORE_DATA, the data buffer provided
                                                        in @ref sd_ble_gap_scan_start is now released. */
  ble_gap_aux_pointer_t     aux_pointer;           /**< The offset and PHY of the next advertising packet in this extended advertising
                                                        event. @note This field is only set if @ref ble_gap_adv_report_type_t::status
                                                        is set to @ref BLE_GAP_ADV_DATA_STATUS_INCOMPLETE_MORE_DATA. */
} ble_gap_evt_adv_report_t;

line 696 ble_gap.h
typedef struct
{
  uint16_t connectable   : 1; /**< Connectable advertising event type. */
  uint16_t scannable     : 1; /**< Scannable advertising event type. */
  uint16_t directed      : 1; /**< Directed advertising event type. */
  uint16_t scan_response : 1; /**< Received a scan response. */
  uint16_t extended_pdu  : 1; /**< Received an extended advertising set. */
  uint16_t status        : 2; /**< Data status. See @ref BLE_GAP_ADV_DATA_STATUS. */
  uint16_t reserved      : 9; /**< Reserved for future use. */
} ble_gap_adv_report_type_t;

line 418 ble_gap.h
#define BLE_GAP_ADV_DATA_STATUS_COMPLETE             0x00 /**< All data in the advertising event have been received. */
#define BLE_GAP_ADV_DATA_STATUS_INCOMPLETE_MORE_DATA 0x01 /**< More data to be received.
                                                               @note This value will only be used if
                                                               @ref ble_gap_scan_params_t::report_incomplete_evts and
                                                               @ref ble_gap_adv_report_type_t::extended_pdu are set to true. */
#define BLE_GAP_ADV_DATA_STATUS_INCOMPLETE_TRUNCATED 0x02 /**< Incomplete data. Buffer size insufficient to receive more.
                                                               @note This value will only be used if
                                                               @ref ble_gap_adv_report_type_t::extended_pdu is set to true. */
#define BLE_GAP_ADV_DATA_STATUS_INCOMPLETE_MISSED    0x03 /**< Failed to receive the remaining data.
                                                               @note This value will only be used if
                                                               @ref ble_gap_adv_report_type_t::extended_pdu is set to true. */

line 199 ble_types.h
typedef struct
{
  uint8_t     *p_data;  /**< Pointer to the data buffer provided to/from the application. */
  uint16_t     len;     /**< Length of the data buffer, in bytes. */
} ble_data_t;

line 280 ble_gap.h
#define BLE_GAP_AD_TYPE_128BIT_SERVICE_UUID_COMPLETE        0x07 /**< Complete list of 128 bit service UUIDs. */
#define BLE_GAP_AD_TYPE_SHORT_LOCAL_NAME                    0x08 /**< Short local device name. */
#define BLE_GAP_AD_TYPE_COMPLETE_LOCAL_NAME                 0x09 /**< Complete local device name. */


